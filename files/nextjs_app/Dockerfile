# 🎓 卒業制作インフラ基盤：検証用スタンドアロン要塞化イメージ
FROM node:22-alpine

WORKDIR /app

# PID 1問題を粉砕する極小Initシステム「tini」だけを導入
RUN apk add --no-cache tini

# ホスト側に一切ファイルを置かないよう、Dockerfileの内部で直接ダミーアプリ（server.js）を生成
RUN echo 'const http = require("http"); \
http.createServer((req, res) => { \
    res.writeHead(200, { "Content-Type": "text/html; charset=utf-8" }); \
    res.end("<h1>🟢 Next.js 15 + Nginx(8080) 2層境界防御ネットワーク 結合検証大成功！</h1>"); \
}).listen(3000); \
console.log("🎓 Next.js 15 ダミーインフラが3000番ポートで自律起動しました。");' > server.js

# 環境変数の定義
ENV NODE_ENV=production
ENV PORT=3000
ENV HOSTNAME="0.0.0.0"

# 最小特権の原則（一般ユーザー実行）
RUN addgroup --system --gid 1001 nodejs && \
    adduser --system --uid 1001 nextjs && \
    chown -R nextjs:nodejs /app

USER nextjs
EXPOSE 3000

# tiniの統括下で安全に永続起動
ENTRYPOINT ["/sbin/tini", "--"]
CMD ["node", "server.js"]
