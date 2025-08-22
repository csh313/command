### session（会话）、Token（令牌）、JWT（JSON Web Token）
用户身份验证与状态管理机制，解决如何确认用户身份并维持会话状态。


#### session
Session 是服务器端保存用户状态的机制，并为用户分配唯一标识（Session ID），用来跟踪用户的状态。
##### 流程：
1. 用户登录，服务器创建一个 Session 对象（存储用户 ID等信息），并生成唯一的 Session ID。
2. 返回Session ID：服务器将 Session ID 通过 Cookie 发送给客户端（浏览器），客户端自动保存 Cookie。
3. 客户端每次发送请求时，会自动携带包含 Session ID 的 Cookie，服务器通过 Session ID 查找对应的 Session 对象，确认用户身份和状态。
4. 服务器端可以设置 Session 的过期时间，当用户长时间不操作时，服务器会自动销毁 Session 对象。
##### 特点：
1. 状态存储在服务器：用户信息（如用户 ID、权限）保存在服务器（内存、数据库、Redis 等），客户端仅持有 Session ID。
2. 依赖 Cookie：Session ID 通常通过 Cookie 传递（也可通过 URL 或请求头传递，但极少用）。
3. 安全性较高：用户核心信息不在客户端暴露，且服务器可主动销毁 Session 强制登出。
缺点：
1. 服务器存储压力大：高并发场景下，大量 Session 占用服务器资源。
2. 分布式难题：在多服务器集群中，需通过 Redis 等中间件实现 Session 共享（否则用户请求到不同服务器时会丢失 Session）。

#### Token
Token 是一种用于认证的令牌，由服务器生成，并通过加密算法签名，发送给客户端。客户端收到 Token 后，可验证签名，并解密获取用户信息。
特点：无状态；不依赖cookie；支持跨域、跨平台
缺点：
1. 服务器存储压力大：Token 存储在客户端，每次请求都需要带上 Token，会增加服务器压力。
2. 无法主动注销：Token 有效期较短，无法主动注销。

#### JWT
JWT（JSON Web Token）是一种基于 JSON 格式的数据结构，用于在各方之间安全地传递信息。
JWT 是一种紧凑、自包含的令牌格式。它包含三个部分：
1. Header（头部）：声明类型和签名算法。
2. Payload（负载）：存放实际需要传递的数据。
3. Signature（签名）：对前两部分进行签名，防止数据被篡改。

##### 流程
1. 用户登录，服务器根据用户信息生成 JWT（组装 Header、Payload，用密钥签名），并返回给客户端。
2. 客户端收到 JWT，保存到本地、Cookie 或内存中，每次请求都携带 JWT。
3. 服务器收到请求，验证 JWT 有效性，并获取用户信息。
4. 服务器可设置 JWT 的过期时间，当用户长时间不操作时，服务器可主动销毁 JWT。
##### 特点
1. 简洁(Compact)：可以通过URL，POST参数或者在HTTP header发送，数据量小，传输速度也很快。
2. 自包含(Self-contained)：负载中包含了所有用户所需要的信息，避免了多次查询数据库；
3. Token是以JSON加密的形式保存在客户端，所以JWT是跨语言的，原则上任何web形式都支持。

1. 紧凑、自包含：Payload 中包含用户核心信息，服务器验证时无需查询数据库（无状态），减轻服务器存储压力。
2. 跨域/跨平台：不依赖 Cookie，可通过请求头传递
3. 无状态：服务端无需存储令牌信息，仅通过验证令牌本身即可完成身份校验
4. 防篡改：通过签名机制确保令牌内容不被篡改
##### 缺点
1. 过期时间限制：需合理设置过期时间（exp），过期后需重新获取令牌。
2. 需合理设置过期时间（exp），过期后需重新获取令牌（如通过 “刷新令牌” 机制）。
3. 若签名密钥泄露，攻击者可伪造 JWT，风险极高。
### jwt
```go
// 传入 JWT 保存的用户信息，返回 token
func (r *authToken) GenerateLoginJwtToken(userInfo *proposal.SessionUserInfo) (string, error) {
	// 创建一个新的令牌对象，指定签名方法为HS256
	token := jwt.New(jwt.SigningMethodHS256)
	// 设置令牌的声明（claims）
	claims := token.Claims.(jwt.MapClaims)
	claims[string(TokenContentKeys.ClusterID)] = userInfo.ClusterID
	claims[string(TokenContentKeys.UserID)] = userInfo.UserID
	claims[string(TokenContentKeys.UserName)] = userInfo.UserName
	// 使用密钥对令牌进行签名
	tokenString, err := token.SignedString([]byte(jwtSecretKey))
	if err != nil {
		return "", err
	}
	return tokenString, nil
}
```

### 解析jwt
```go

func (p *TokenValidator) ParseJwtToken(req AuthReq, tokenString string) AuthResult {
	token, err := jwt.Parse(tokenString, func(token *jwt.Token) (interface{}, error) {
		// 验证签名方法是否与生成时一致
		if _, ok := token.Method.(*jwt.SigningMethodHMAC); !ok {
			return nil, fmt.Errorf("unexpected signing method: %v", token.Header["alg"])
		}

		// 返回用于签名的密钥
		return []byte(jwtSecretKey), nil
	})
	if err != nil {
		return NewAuthResult(req.GetCtx()).WithBusErr(
			apicore.Error(apicode.AuthTokenValidateError, err, "签名方法验证失败")).
			WithSuccess(false)
	}

	// 验证 token 是否有效
	if !token.Valid {
		// "invalid token"
		return NewAuthResult(req.GetCtx()).WithBusErr(
			apicore.ErrorWithDebugMsg(apicode.AuthTokenValidateError, "非法Token")).
			WithSuccess(false)
	}

	// 提取 claims
	claims, ok := token.Claims.(jwt.MapClaims)
	if !ok {
		// "could not parse token claims"
		return NewAuthResult(req.GetCtx()).WithBusErr(
			apicore.ErrorWithDebugMsg(apicode.AuthTokenValidateError, "解析token claims失败")).
			WithSuccess(false)
	}

	userInfo, authResult := p.ExtractUserInfo(req, claims)
	if !authResult.IsSuccess() {
		return authResult
	}

	return NewAuthResult(req.GetCtx()).WithUserInfo(userInfo).WithSuccess(true)
}

func (p *TokenValidator) ExtractUserInfo(req AuthReq, claims jwt.MapClaims,
) (*proposal.SessionUserInfo, AuthResult) {

	// 提取用户信息
	idInterface, exists := claims[string(TokenContentKeys.UserID)]
	if !exists {
		// "token claims missing 'id'"
		return nil, NewAuthResult(req.GetCtx()).WithBusErr(
			apicore.ErrorWithDebugMsg(apicode.AuthTokenValidateError, "解析token user失败")).
			WithSuccess(false)
	}

	// 将 map 转换为 JSON 字节
	idFloat64, ok := idInterface.(float64)
	if !ok {
		// "could not parse 'id' to uint"
		return nil, NewAuthResult(req.GetCtx()).WithBusErr(
			apicore.ErrorWithDebugMsg(apicode.AuthTokenConvertError, "转化token信息失败")).
			WithSuccess(false)
	}
	id := uint(idFloat64)

	// 提取用户信息
	userNameInterface, exists := claims[string(TokenContentKeys.UserName)]
	if !exists {
		// "token claims missing 'userName'"
		return nil, NewAuthResult(req.GetCtx()).WithBusErr(
			apicore.ErrorWithDebugMsg(apicode.AuthTokenValidateError, "解析token user失败")).
			WithSuccess(false)
	}

	// 将 map 转换为 JSON 字节
	userName, ok := userNameInterface.(string)
	if !ok {
		// "could not parse 'userName' to int32"
		return nil, NewAuthResult(req.GetCtx()).WithBusErr(
			apicore.ErrorWithDebugMsg(apicode.AuthTokenConvertError, "转化token信息失败")).
			WithSuccess(false)
	}

	return &proposal.SessionUserInfo{
		UserID:   id,
		UserName: userName,
	}, NewAuthResult(req.GetCtx()).WithSuccess(true)
}
```
