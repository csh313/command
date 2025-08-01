func buildCreateUserCmdFunc(buildCmdArgs interface{}) (string, []string, core.BusinessError) {
	businessErr := core.Error(
		http.StatusBadRequest,
		code.ExecBuildCmdError,
		code.Text(code.ExecBuildCmdError))
	if buildCmdArgs == nil {
		return "", nil, businessErr.WithError(fmt.Errorf("命令行参数为空"))
	}
	cmdArgs, err := apiutils.ConvertAttachments[operate.InsideAttachments](buildCmdArgs)
	if err != nil {
		return "", nil, businessErr.WithError(fmt.Errorf("node.Attachment转为对应参数类型失败"))
	}
	var args = []string{"-M", "-u", cmdArgs.UserInfo.UID, "-g", cmdArgs.UserInfo.GID, fmt.Sprintf(`"%s"`, cmdArgs.UserInfo.Username)}
	return exec.BIN.USERADD.String(), args, nil
}

type UserInfo struct {
	Username    string `json:"username"`
	UID         string `json:"uid"`
	GID         string `json:"gid"`
	NewPassword string `json:"new_password"`
	GroupName   string `json:"group_name"`
	// 其他字段
}

type InsideAttachments struct {
	UserInfo UserInfo `json:"user_info"`
}