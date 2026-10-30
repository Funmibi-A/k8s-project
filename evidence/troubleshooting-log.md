# Troubleshooting Log

## Issue 1: ImagePullBackOff (bad image tag)
- **Symptom:** A new Pod (`hello-deploy-887f968f6-j9jq9`) stayed `0/1` and went from `ContainerCreating` to `ImagePullBackOff`. The three original Pods kept running.
- **Commands used:** `kubectl get pods`, `kubectl describe pod hello-deploy-887f968f6-j9jq9`, `kubectl rollout status deployment/hello-deploy`
- **Root cause:** The image tag `nginx:1.99-typo` does not exist in the registry (`manifest for nginx:1.99-typo not found`).
- **Fix:** Changed the image back to `nginx:1.27` and re-applied the manifest. The rollout completed successfully.
- **Lesson:** The rolling update kept the healthy Pods serving traffic while the new Pod failed.

## Issue 2: Pod stuck in Pending (unschedulable memory request)
- **Symptom:** A new Pod (`hello-deploy-5c7cf6fd48-d6xxh`) stayed in `Pending` and never started, while the healthy Pods kept running.
- **Commands used:** `kubectl get pods`, `kubectl describe pod hello-deploy-5c7cf6fd48-d6xxh`
- **Root cause:** The manifest requested `64Gi` of memory. The Events showed `FailedScheduling: 0/1 nodes are available: 1 Insufficient memory`.
- **Fix:** Reset the request and limit to `64Mi` and `128Mi` and re-applied the manifest. All three Pods returned to `Running`.
- **Lesson:** The scheduler places Pods by resource requests, not actual usage.
