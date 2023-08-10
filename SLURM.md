# SLURM

#### VS code remote to Jupyter Notebook on SLURM

This refers to [Running Jupyter on Slurm GPU Nodes (stanford.edu)](https://nero-docs.stanford.edu/jupyter-slurm.html) and [VS code docs](https://code.visualstudio.com/docs/python/jupyter-support-py)

In VS code, ssh to the server and set up jupyter notebook on a computing node:

```bash
#Request a computing node on the cluster
srun -p cpu -N 1 -n 40 --pty bash
#set up jupyter notebook
jupyter notebook --ip 0.0.0.0 --port 8888
```

Will get output like:

```bash
[I 15:37:41.813 NotebookApp] Jupyter Notebook 6.4.0 is running at:
[I 15:37:41.813 NotebookApp] http://NODENAME:8888/?token=xxx
[I 15:37:41.813 NotebookApp]  or http://127.0.0.1:8888/?token=xxx
[I 15:37:41.813 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
```

`Ctrl+Shift+P` and run "Jupyter: Specify local or remote Jupyter server for connections". Use the URL **http://NODENAME:8888/?token=xxx** not 127.0.0.1 to connect to the server.

#### Parallelize multi-threaded jobs

For a multi-threaded job _example.sh_

```bash
#!/bin/bash

seq 1 100 | xargs -P 20 -I {} echo "No. {}"
```

Suppose we have a cluster with 40 cores per node. We want to parallelize 4 _example.sh_ on SLURM. 

```bash
#!/bin/bash

#SBATCH -p partition
#SBATCH -N 2 -n 4 -c 20

parallel -j $SLURM_NTASKS '
srun -N 1 -n 1 -c 20 bash example.sh
'
```

##### Template

Input files of each sample are saved in a subfolder under input path. Here, we pass the sample folder, input path and output path to the _test.sh_. The _test.sh_ will create a ${sample}.done to label finished samples. Log file for each sample will be saved in log path. 

```bash
#!/bin/bash

#SBATCH -p cpu
#SBATCH -N 10
#SBATCH -n 40
#SBATCH -c 10

inpath=/example/input/
outpath=/example/output/
finish=/example/finish/
logpath=/example/log/

[[ ! -d $logpath ]] && mkdir -p $logpath
[[ ! -d $outpath ]] && mkdir -p $outpath
[[ ! -d $finish ]] && mkdir -p $finish

ls $inpath | parallel -j $SLURM_NTASKS '
sample={1}
inpath={2}
outpath={3}
logpath={4}
sample=$(basename $sample)
if [[ ! -e /example/finish/${sample}.done  ]]; then
    srun -N 1 -n 1 -c 10 -o ${logpath}${sample}.log bash test.sh $sample $inpath $outpath 
fi
' :::: - ::: $inpath ::: $outpath ::: $logpath
```

#### Conditional job submission

- Create a flag for finished job. Test the flag when submit jobs:
  
  run.sh
  
  ```bash
  COMMAND TO RUN
  [[ $? -eq 0 ]] && touch finish/JOBNAME.done
  ```
  
  submit.sh
  
  ```bash
  [[ ! -e finish/JOBNAME.done ]] && srun -N 1 -n 1 bash run.sh
  ```

- Label both finished and error jobs:
  
  run.sh:
  
  ```bash
  COMMAND TO RUN
  if [[ $? -eq 0 ]]; then 
      # always put error fix before mark finished, otherwise return exit code 1 if at end
      [[ -e "error/JOBNAME.error" ]] && mv "error/sample.error" "error/sample.error_fixed"
      touch "finish/JOBNAME.done"
  else
      touch "error/sample.error"
  fi
  ```
  
  submit.sh
  
  ```bash
  [[ ! -e finish/JOBNAME.done ]] && srun -N 1 -n 1 bash run.sh
  ```

- Submit job when two files are different
  
  ```bash
  ! cmp -s FILE1 FILE2 &&    srun -N 1 -n 1 bash run.sh
  ```

- Update analysis: Submit job when the input file is more recent than output file
  
  ```bash
  [[ INPUT -nt OUTPUT ]] && srun -N 1 -n 1 bash run.sh
  ```
