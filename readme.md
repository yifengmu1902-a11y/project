cd /home/featurize/work/biological_prior_validation
conda create -n sciplex-dualgraph python=3.11 pip -y
conda activate sciplex-dualgraph
python -m pip install pandas numpy scipy pyarrow jupyterlab ipykernel duckdb fastparquet scikit-learn faiss-cpu matplotlib networkx reportlab nbformat nbclient

python -m pip check
python -c "import pandas, numpy, scipy, pyarrow, duckdb, fastparquet, sklearn, faiss, matplotlib, networkx, reportlab, nbformat, nbclient; print('Python dependencies OK')"

python -m ipykernel install --user --name sciplex-dualgraph --display-name "Python (sciplex-dualgraph)"

mkdir -p configs
python -m pip freeze > configs/python_requirements.lock.txt
conda env export --no-builds > configs/sciplex-dualgraph.yml
(
  set -e
  cd /home/featurize/work/biological_prior_validation

  conda create -n sciplex-rmeta \
    --override-channels -c conda-forge -c bioconda \
    --strict-channel-priority \
    r-base r-monocle3 \
    bioconductor-summarizedexperiment bioconductor-biobase \
    gzip bzip2 xz -y

  conda run -n sciplex-rmeta Rscript -e \
    'library(monocle3); library(SummarizedExperiment); library(Biobase); cat("R dependencies OK\n"); sessionInfo()'

  mkdir -p configs
  conda env export -n sciplex-rmeta --no-builds \
    > configs/sciplex-rmeta.yml
)
