ARG NODE_VERSION=20-alpine
FROM node:${NODE_VERSION}

WORKDIR /app

# 下一步由 agent 生成 package.json 后生效
COPY . .

EXPOSE 3000
CMD ["sh", "-c", "echo 'Web scaffold not generated yet. Please run agent scaffold step.' && sleep infinity"]
