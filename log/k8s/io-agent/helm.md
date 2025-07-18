## deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "io-agent.fullname" . }}
  labels:
    {{- include "io-agent.labels" . | nindent 4 }}
spec:
  {{- if not .Values.autoscaling.enabled }}
  replicas: {{ .Values.replicaCount }}
  {{- end }}
  selector:
    matchLabels:
      {{- include "io-agent.selectorLabels" . | nindent 6 }}
  template:
    metadata:
      {{- with .Values.podAnnotations }}
      annotations:
        {{- toYaml . | nindent 8 }}
      {{- end }}
      labels:
        {{- include "io-agent.labels" . | nindent 8 }}
        {{- with .Values.podLabels }}
        {{- toYaml . | nindent 8 }}
        {{- end }}
    spec:
      initContainers:
        - name: wait-for-dependent-pods
          image: cr.io.plus:80/library/bitnami/kubectl:1.31.3 # 使用带有kubectl命令的镜像
          command:
          - /bin/sh
          - -c
          args:
          - |
            echo "io-agent waiting for dependent pods(db, redis) to be ready...";
            kubectl wait --for=condition=ready pod -l app.kubernetes.io/name=io-db;
            kubectl wait --for=condition=ready pod -l app.kubernetes.io/name=io-redis;
            cp /config/pro_configs.toml /config-rw/pro_configs.toml;
          volumeMounts:
            - name: config
              mountPath: /config
            - name: config-rw
              mountPath: /config-rw
      {{- with .Values.imagePullSecrets }}
      imagePullSecrets:
        {{- toYaml . | nindent 8 }}
      {{- end }}
      serviceAccountName: {{ include "io-agent.serviceAccountName" . }}
      securityContext:
        {{- toYaml .Values.podSecurityContext | nindent 8 }}
      containers:
        - name: {{ .Chart.Name }}
          securityContext:
            {{- toYaml .Values.securityContext | nindent 12 }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag | default .Chart.AppVersion }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          command: {{- toYaml .Values.command | nindent 12 }}
          args: {{- toYaml .Values.args | nindent 12 }}
          ports:
            {{- range .Values.service.ports }}
            {{- if eq .name "http" }}
            - containerPort: {{ .port }}
              name: http
              protocol: TCP
            {{- end }}
            {{- end }}
          readinessProbe:
            {{- toYaml .Values.readinessProbe | nindent 12 }}
          livenessProbe:
            {{- toYaml .Values.livenessProbe | nindent 12 }}
          env:
            {{- toYaml .Values.env | nindent 12 }}
          resources:
            {{- toYaml .Values.resources | nindent 12 }}
          volumeMounts:
            - name: config-rw
              mountPath: /app/configs/pro_configs.toml
              subPath: pro_configs.toml
          # volumeMounts:
          # {{- with .Values.volumeMounts }}
          #   {{- toYaml . | nindent 12 }}
          # {{- end }}
          #   - name: config
          #     mountPath: /app/configs/pro_configs.toml
          #     # mountPath: /build/configs/pro_configs.toml
          #     subPath: pro_configs.toml
          #     # readOnly: true
      volumes:
        - name: config
          configMap:
            name: {{ include "io-agent.fullname" . }}-configmap
            defaultMode: 511
        - name: config-rw
          emptyDir: {}
      # volumes:
      # {{- with .Values.volumes }}
      #   {{- toYaml . | nindent 8 }}
      # {{- end }}
      #   - name: config
      #     configMap:
      #       defaultMode: 511
      #       name: {{ include "io-agent.fullname" . }}-configmap
      {{- with .Values.nodeSelector }}
      nodeSelector:
        {{- toYaml . | nindent 8 }}
      {{- end }}
      {{- with .Values.affinity }}
      affinity:
        {{- toYaml . | nindent 8 }}
      {{- end }}
      {{- with .Values.tolerations }}
      tolerations:
        {{- toYaml . | nindent 8 }}
      {{- end }}


### q1
  cp /config/pro_configs.toml /config-rw/pro_configs.toml;
volumeMounts:
  - name: config
    mountPath: /config
  - name: config-rw
    mountPath: /config-rw
volumeMounts:
    - name: config-rw
      mountPath: /app/configs/pro_configs.toml
      subPath: pro_configs.toml

volumes:
  - name: config
    configMap:
      name: {{ include "io-agent.fullname" . }}-configmap
      defaultMode: 511
  - name: config-rw
    emptyDir: {}

[root@mstor01 helm]# k logs io-agent-7869f6c8bf-85snl -n dev-io-agent
Defaulted container "io-agent" out of: io-agent, wait-for-dependent-pods (init)
/build/configs/pro_configs.toml
2025/07/18 10:57:51 警告：读取 配置文件 /build/configs/pro_configs.toml 失败，使用默认配置: open /build/configs/pro_configs.toml: no such file or directory
config file: /build/configs/pro_configs.toml
2025/07/18 10:57:51 /build/internal/repository/mysql/mysql.go:97
[error] failed to initialize database, got error dial tcp 127.0.0.1:3306: connect: connection refused

### q2

readinessProbe:   #只影响流量分发,不会重启pod
  httpGet:
    path: /health
    port: 80
  initialDelaySeconds: 5 # 初始延迟，给服务时间启动
  periodSeconds: 5
  timeoutSeconds: 1
  failureThreshold: 10


livenessProbe:   #会自动重启 失败 pod
  httpGet:
    path: /health
    port: 80
  initialDelaySeconds: 120  # 初始延迟，给服务时间启动
  periodSeconds: 3
  timeoutSeconds: 1         # 每次检查最多等待1秒
  failureThreshold:  100     #debug 环境为代码检查多保留点时间
  
报错：  Warning  Unhealthy  13s (x19 over 98s)     kubelet            Readiness probe failed: Get "http://10.244.0.181:80/health": dial tcp 10.244.0.181:80: connect: connection refused

