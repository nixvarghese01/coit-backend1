# coit-backend1: Sentiment Analysis Web API

A Spring Boot service that sits between the React frontend and the Python sentiment-analysis logic service. It accepts a sentence, forwards it to the logic API and returns the polarity.

The app is a three-tier sentiment-analysis demo: you type a sentence in the web UI and it returns a polarity score.

```
React frontend  --POST /sentiment-->  Spring Boot web API  --POST /analyse/sentiment-->  Python (Flask + TextBlob)
   (nginx :80)                            (:8080)                                         (:5000)
```

## Tech stack
- Java 8, Spring Boot 1.5
- Maven (wrapper included)
- Docker (single-stage and multi-stage Dockerfiles)

## API
| Method | Path | Description |
|---|---|---|
| POST | `/sentiment` | Body `{"sentence": "..."}`. Returns `{"sentence": "...", "polarity": 0.5}` |
| GET | `/testHealth` | Health check |

## Build and run locally
```bash
./mvnw clean install
java -jar target/sentiment-analysis-web-0.0.2-SNAPSHOT.jar --sa.logic.api.url=http://localhost:5000
```

## Docker
```bash
# multi-stage: builds the jar inside the image
docker build -f Dockerfile-multistage -t <dockerhub-user>/sentiment-analysis-web-app .

# single-stage: needs the jar built first (./mvnw install)
docker build -f Dockerfile -t <dockerhub-user>/sentiment-analysis-web-app .

docker run -d -p 8080:8080 -e SA_LOGIC_API_URL=http://<logic-host>:5000 <dockerhub-user>/sentiment-analysis-web-app
```

`SA_LOGIC_API_URL` must point to the Python logic service. When both run as containers, put them on the same Docker network and use the container name, for example `http://sa-logic:5000`.

## Branches
- `main`: default branch
- `master`: older version

## Related repos
- [coit-frontend](https://github.com/nixvarghese01/coit-frontend): React UI
- [coit-simple-micro-GA](https://github.com/nixvarghese01/coit-simple-micro-GA): all three services plus Kustomize and GitHub Actions CI/CD
