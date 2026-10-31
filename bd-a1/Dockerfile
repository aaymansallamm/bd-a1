FROM quay.io/jupyter/scipy-notebook:latest

USER root
RUN mkdir -p /home/jovyan/bd-a1/output/figures \
    && chown -R ${NB_UID}:${NB_GID} /home/jovyan/bd-a1
USER ${NB_UID}

WORKDIR /home/jovyan/bd-a1

COPY --chown=${NB_UID}:${NB_GID} requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

COPY --chown=${NB_UID}:${NB_GID} data/ ./data/
COPY --chown=${NB_UID}:${NB_GID} notebook/ ./notebook/

ENV OMP_NUM_THREADS=2 OPENBLAS_NUM_THREADS=2 MKL_NUM_THREADS=2

EXPOSE 8888
CMD ["start-notebook.py", "--ServerApp.ip=0.0.0.0", "--ServerApp.root_dir=/home/jovyan/bd-a1"]
