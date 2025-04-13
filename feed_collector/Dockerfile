# ビルドステージ
FROM golang:1.24.2 AS builder

# 作業ディレクトリの設定
WORKDIR /app

# 依存関係ファイルをコピー
COPY go.mod go.sum ./
RUN go mod download

# ソースコードをコピー
COPY . .

# アプリケーションをビルド
RUN CGO_ENABLED=0 GOOS=linux go build -o feed_collector .

# 実行ステージ
FROM alpine:latest

# 必要なパッケージのインストール
RUN apk --no-cache add ca-certificates tzdata

# 作業ディレクトリの設定
WORKDIR /app

# ビルドステージからバイナリをコピー
COPY --from=builder /app/feed_collector .

# 必要なディレクトリを作成
RUN mkdir -p /data

# 実行ユーザーの設定
RUN adduser -D -g '' appuser
USER appuser

# ポートの公開
EXPOSE 9000

# ヘルスチェックのためのメタデータ
HEALTHCHECK --interval=30s --timeout=30s --start-period=5s --retries=3 \
  CMD wget -q -O - http://localhost:9000/health || exit 1

# アプリケーションの実行
CMD ["./feed_collector"]