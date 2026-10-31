# Evidence index

## Actual supplied screenshot

- `07-notebook-output-checks.png`: copied unchanged from the user's `big data project.png`.
  Shows the notebook's required-output checks passing and the nine saved figure names.

## Images rendered from genuine terminal transcripts

The PNGs below are clearly labeled **TRANSCRIPT EXCERPT**, not live terminal screenshots.
They are deterministic visual renderings of the user's pasted commands/output. They do not
represent fresh execution, and no missing successful command result has been invented.
Matching `.txt` excerpts and the sanitized full transcript are included for traceability.
Where output has been shortened, omissions are visibly marked. Jupyter login tokens and
the registry upload session URL were redacted. Other line wrapping was preserved.

- `01-build-success-transcript.png`: Successful final image build. Source: supplied transcript, lines 158-218; excerpt with omission marked.
- `03-running-container-transcript.png`: Running assignment container. Source: supplied transcript, lines 60-67; original line wraps retained.
- `04-stopped-container-transcript.png`: Container lifecycle: stopped container. Source: supplied transcript, lines 128-137; original line wraps retained.
- `05-jupyter-startup-transcript.png`: JupyterLab startup inside Docker. Source: supplied transcript, command on line 1 and lines 40-54; tokens redacted.
- `06-bind-mount-transcript.png`: Output bind mount. Source: complete docker inspect output supplied earlier in this chat; terminal line wrap joined.
- `09-host-output-transcript.png`: Results saved on the Windows host. Source: supplied transcript, lines 83-116; original listing retained.
- `10-persistence-transcript.png`: Output files survive container removal. Source: supplied transcript, lines 138-157.
- `11-push-failed-transcript.png`: Docker Hub publication attempt: failed. Source: supplied transcript, lines 372-408; documents failure, not publication.

## Still missing for the rubric

- An actual screenshot of `docker images` showing `bd-a1:1.0`. That command's output was
  not present in the supplied transcript. The assistant could not query the Docker engine
  because its environment was denied access. Run `docker images bd-a1` and capture it.
- Actual terminal screenshots for the rendered transcript entries if the TA requires
  screenshots rather than transcript excerpts. The images here do not claim to replace
  that requirement without the TA accepting this format.
- Successful Docker Hub push/digest and another teammate's pull/run evidence remain pending.
  `11-push-failed-transcript.png` documents the failure; it is not proof of a successful push.

The bulk transcript's first `docker ps -a` listing and inspect JSON are incomplete. The
stopped-container excerpt uses the later, more complete `docker ps -a` output; the mount
excerpt uses the complete earlier user message in this same conversation.
