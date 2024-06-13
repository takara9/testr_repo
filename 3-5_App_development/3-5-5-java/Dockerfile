FROM eclipse-temurin:21.0.3_9-jre

# アプリケーションのインストール
RUN mkdir /app
WORKDIR /app
COPY --chown=65534:65534 target/rest-service-1.0.0.jar /app/rest-service.jar

# コンテナの設定
USER 65534:65534
EXPOSE 8080
CMD ["java", "-jar", "/app/rest-service.jar"]

