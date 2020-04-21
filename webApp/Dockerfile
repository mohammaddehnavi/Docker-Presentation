# this is docker file for run web app with nodeJS
FROM node:alpine
WORKDIR /mohammad/webapp
COPY ./package.json ./
RUN npm install
COPY ./ ./

CMD ["npm", "start"]
