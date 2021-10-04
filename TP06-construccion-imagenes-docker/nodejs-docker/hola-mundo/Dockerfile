
FROM node:13.12.0-alpine AS dependencias
COPY package.json /tmp/
WORKDIR /tmp/
RUN npm install
 

FROM dependencias
COPY --from=DEPENDENCIAS /tmp/node_modules ./node_modules
COPY . .
EXPOSE 3000
CMD npm run start
