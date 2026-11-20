# MAD Module — DevOps Practical (Phases 1–6)

This project implements the **six phases** in the supplied practical: Git/GitHub, GitHub Actions CI, Docker, Kubernetes/Minikube, self-healing demonstration, and Render cloud deployment.

## Files

| File | Purpose |
| --- | --- |
| `app.py` | Final Phase 6 Flask app with cloud-friendly `PORT` and V2 response |
| `test_app.py` | Automated tests matching **current final app** |
| `requirements.txt` | Flask and pytest dependency versions from practical |
| `.github/workflows/ci.yml` | CI on push to `main` and PRs to `main` |
| `Dockerfile` | Python 3.9 container for Flask app |
| `k8s/deployment.yaml` | Two Kubernetes app replicas |
| `k8s/service.yaml` | NodePort exposure |
| `stages/*.txt` | Original V1 / initial Render / final V2 stage files |
| `README_SINHALA.md` | Step-by-step practical instructions (Sinhala) |
| `EVIDENCE_CHECKLIST.md` | Which screenshots to capture after real deployment |

**Important:** The root files represent the *final V2 submission state*. To perform the lab in the lecturer's order, copy the corresponding example pairs from `stages/` into `app.py` and `test_app.py` at each phase (instructions in `README_SINHALA.md`). The automated test must change whenever the expected homepage message changes; otherwise CI fails.

## Quick start

Windows PowerShell:

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
pytest -q
python app.py
```

Open http://localhost:5000 and expect `CI/CD and Docker Pipeline is a massive success! V2`.

If PowerShell blocks activation, you can use `.\.venv\Scripts\python.exe -m pip install -r requirements.txt` and `.\.venv\Scripts\python.exe -m pytest -q` instead.

## Repository upload

Create an empty GitHub repo named `devops-practical` (no README added by GitHub). Run:

```powershell
git init
git add .
git commit -m "Complete MAD DevOps practical"
git branch -M main
git remote add origin https://github.com/YOUR_GITHUB_USERNAME/devops-practical.git
git push -u origin main
```

The worksheet has a repository name mismatch (`devops-practical` vs `devops-masterclass` in its remote URL). **Use the repo name you actually created**; example above consistently uses `devops-practical`.

## Docker Hub / Kubernetes

```powershell
docker build -t flask-app:v1 .
docker run --rm -p 5000:5000 flask-app:v1
# Stop with Ctrl+C

docker login
docker tag flask-app:v1 YOUR_DOCKERHUB_USERNAME/flask-app:v1
docker push YOUR_DOCKERHUB_USERNAME/flask-app:v1
```

**Replace** `your-dockerhub-username` in `k8s/deployment.yaml` with your own Docker Hub username (lowercase) before applying.

```powershell
minikube start --driver=docker
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
kubectl get pods
minikube service flask-service
# For self-healing:
kubectl delete pod ACTUAL_POD_NAME
kubectl get pods -w
# Press Ctrl+C to stop watching
```

## Render

Link the GitHub repo to a Render **Web Service**, choose **Docker**, **main** branch, and an available Free instance if suitable. Keep Git auto-deploy enabled, ideally with **After CI Checks Pass** where available. Because the app binds to `0.0.0.0` and `PORT`, it works with Render's assigned HTTP port. Push a new homepage message (and update the test) to show redeployment.

**External actions not run as part of this file creation:** publishing a GitHub repo, Docker image upload, Minikube execution, and Render service creation require your machine/accounts. See `EVIDENCE_CHECKLIST.md`.
