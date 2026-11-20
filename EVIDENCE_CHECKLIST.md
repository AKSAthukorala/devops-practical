# Practical Demonstration — Evidence Checklist

Capture **real screenshots** after running each phase. Do not report a command as completed until you have actually run it.

- [ ] Phase 1: `pytest -q` passes and local `http://localhost:5000` shows V1.
- [ ] Phase 1: GitHub public repository `devops-practical` shows pushed files.
- [ ] Phase 2: GitHub **Actions > CI Pipeline** shows successful run on `main`.
- [ ] Phase 3: Docker build success and browser shows Flask app from container.
- [ ] Phase 3: Docker Hub repository `<username>/flask-app:v1` shows public image.
- [ ] Phase 4: `kubectl get pods` shows **two Running** pods.
- [ ] Phase 4: browser opens application through `minikube service flask-service`.
- [ ] Phase 5: `kubectl delete pod` + `kubectl get pods -w` shows replacement pod.
- [ ] Phase 6: Render connected GitHub repo, deploy status **Live** and public URL shows LIVE text.
- [ ] Phase 6: after V2 commit/push, GitHub Actions passes and Render live URL shows new V2 text.

## Submission Details — Fill in after deployment

**GitHub Repository:** `https://github.com/<github-username>/devops-practical`

**Docker Hub Image:** `https://hub.docker.com/r/<dockerhub-username>/flask-app`

**Render Public URL:** `https://<actual-service-name>.onrender.com`

**CI Status:** _Pending real GitHub Actions run_

**Kubernetes Status:** _Pending local Minikube run_

**Render Status:** _Pending real Render deployment_

## Explanation for viva

- **Git:** stores and tracks project code changes.
- **CI:** automatically runs pytest after pushes/PRs, catching failures early.
- **Docker:** packages app + requirements into a reproducible container image.
- **Kubernetes Deployment:** requests two replicas and recreates a lost pod to restore desired state.
- **Kubernetes Service:** exposes pods through a stable network endpoint.
- **Render CD:** builds and publishes the service when changes arrive from the connected GitHub branch; configure to wait for CI checks if offered.
