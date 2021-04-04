# Submit job

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

