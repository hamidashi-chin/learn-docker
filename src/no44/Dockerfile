FROM ubuntu:20.04
RUN apt-get update -y && \
    apt-get install -y nginx
ENV TESTENV="hamidashi-chin"
ENV APP_ENV="production"
# よく使うのが、local, production
CMD ["nginx", "-g", "daemon off;"]
