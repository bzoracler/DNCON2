# DNCON2

**Deep convolutional neural networks for protein contact map prediction**   

Web-server and datasets at http://sysbio.rnet.missouri.edu/dncon2/  

Citation
--------------------------------------------------------------------------------------
Badri Adhikari, Jie Hou, Jianlin Cheng. "DNCON2: Improved protein contact prediction using two-level deep convolutional neural networks". Bioinformatics, 2017.

Test Environment
--------------------------------------------------------------------------------------
64-bit PC - Ubuntu 16.04 LTS

Programs, Scripts, and Databases dependencies
--------------------------------------------------------------------------------------
![Programs, Scripts, and Databases dependencies](https://github.com/multicom-toolbox/DNCON2/blob/master/dependency.PNG)

Installation Notes
--------------------------------------------------------------------------------------
- We tested in Ubuntu becasue the tool 'FreeContact' is easier to install in a Debian system. If you would like to install DNCON2 in some other operating systems, first test if 'FreeContact' can be installed in it. If, for some reason, you do not have a Ubuntu machine, and cannot install FreeContact, you can still use DNCON2. With just a few code updates you can skip using the FreeContact tool. You will get slightly less precise results.
- Updated versions of Databases and Programs 'may' generate better results, but we recommend initial installation with the versions suggested here. We haven't rigorously tested new versions of third-party programs and newer databases.
- Since these installation steps are for a 64-bit machine, for installing some of the programs, your links may be different. Please refer to appropriate third-party websites.
- For verifying your installation, use the results in the scripts and outputs in the dry-run directory. In the dry-run directory we provide input, output, and log files of DNCON2 execution for three sequences - 3e7u, T0866, and T0900.
- It is possible that your installation results have slightly different confidence values. We noticed that the programs PSICOV, CCMpred, and FreeContact can produce slightly different results based on the machine architecture, availability/absence of GPU, etc.

Data Flow
--------------------------------------------------------------------------------------
![Data Flow](https://github.com/multicom-toolbox/DNCON2/blob/master/dataflow.PNG)


Installation Steps
--------------------------------------------------------------------------------------

**(A) Download and Unzip DNCON2 package**  
Create a working directory called 'DNCON2' where all scripts, programs and databases will reside:
```
cd ~
mkdir DNCON2
```
Download the DNCON2 code:
```
cd ~/DNCON2/
wget http://sysbio.rnet.missouri.edu/bdm_download/dncon2-tool/DNCON2.tar.gz
tar zxvf DNCON2.tar.gz
# Alternately
git clone https://github.com/multicom-toolbox/DNCON2.git
```

**(B) Download and Unzip all databases**  
```
cd ~/DNCON2/  
mkdir databases  
cd databases/  
wget http://sysbio.rnet.missouri.edu/bdm_download/dncon2-tool/databases/nr90-2012.tar.gz  
tar zxvf nr90-2012.tar.gz  
wget http://sysbio.rnet.missouri.edu/bdm_download/dncon2-tool/databases/uniref.tar.gz  
tar zxvf uniref.tar.gz  
wget http://sysbio.rnet.missouri.edu/bdm_download/dncon2-tool/databases/uniprot20_2016_02.tar.gz  
tar zxvf uniprot20_2016_02.tar.gz  
```

**(C) Install FreeContact, PSICOV, and CCMpred**  
```
sudo apt-get install freecontact
```
Note: If you do not have root permissions, refer to the 'freecontact-install-non-root.txt' for instructions.
```
cd ~/DNCON2/
mkdir psicov
cd psicov/
wget http://bioinfadmin.cs.ucl.ac.uk/downloads/PSICOV/psicov2.c
wget http://bioinfadmin.cs.ucl.ac.uk/downloads/PSICOV/Makefile
make
```
```
cd ~/DNCON2/
sudo apt install git
sudo apt install cmake
git clone --recursive https://github.com/soedinglab/CCMpred.git
cd CCMpred
cmake .
make
```

**[OPTIONAL] Verify FreeContact, PSICOV, and CCMpred Installation**  
```
cd ~/DNCON2/
mkdir test-dncon2
./CCMpred/bin/ccmpred ./CCMpred/example/1atzA.aln ~/DNCON2/test-dncon2/ccmpred.cmap
./psicov/psicov ./CCMpred/example/1atzA.aln > ~/DNCON2/test-dncon2/psicov.rr
freecontact < ./CCMpred/example/1atzA.aln > ~/DNCON2/test-dncon2/freecontact.rr
[above freecontact command may throw a 'Symbol .. has different size ..' warning!]
```

**(D) Install Tensorflow, Keras, and h5py and Update keras.json**  

(a) Install Tensorflow: 
```
sudo pip install tensorflow
```
GPU version is NOT needed. If you face issues, refer to the the tensor flow installation guide at https://www.tensorflow.org/install/install_linux.

(b) Install Keras:
```
sudo pip install keras
```
(c) Install the h5py library:  
```
sudo pip install h5py
```

(d) Add the entry [“image_dim_ordering": "tf”,] to your keras..json file at ~/.keras/keras.json. Note that if you have not tried to run Keras before, you have have to execute the Tensorflow verification step once so that your keras.json file is created. After the update, your keras.json should look like the one below:  
```
{
    "epsilon": 1e-07,
    "floatx": "float32",
    "image_dim_ordering":"tf",
    "image_data_format": "channels_last",
    "backend": "tensorflow"
}
```

**[OPTIONAL] Verify Tensorflow, Keras, and hp5y installation**  

The script ‘predict-rr-from-features.sh’ takes a feature file as input and predicts contacts using the trained CNN models. Using an existing feature file (feat-3e7u.txt) and a name for output RR file and intermediate stage2 feature file, test the installation of Tensorflow, Keras, and hp5y using the following command:
```
cd ~/DNCON2/
./DNCON2/scripts/predict-rr-from-features.sh ./DNCON2/dry-run/output/3e7u-2017-Oct-23/feat-3e7u.txt ./test-dncon2/3e7u.rr ./test-dncon2/feat-stg2.txt
```
Verify that the contents of your output file ‘3e7u.rr’ matches the contents of ‘~/DNCON2/dry-run/output/3e7u-2017-Oct-23/3e7u.dncon2.rr’.

**(E) Install Legacy Blast, PSIPRED, and runpsipredandsolv (MetaPSICOV)**  

(a) Install PSIPRED
```
cd ~/DNCON2/
wget http://bioinfadmin.cs.ucl.ac.uk/downloads/psipred/old_versions/psipred3.5.tar.gz
tar zxvf psipred3.5.tar.gz
```
(b) Install Legacy Blast
```
wget ftp://ftp.ncbi.nlm.nih.gov/blast/executables/legacy.NOTSUPPORTED/2.2.26/blast-2.2.26-x64-linux.tar.gz
tar zxvf blast-2.2.26-x64-linux.tar.gz
```
(c) Install MetaPSICOV
```
mkdir ~/DNCON2/metapsicov
cd  ~/DNCON2/metapsicov/
wget http://bioinfadmin.cs.ucl.ac.uk/downloads/MetaPSICOV/old/v1/metapsicov.tar.gz
tar zxvf metapsicov.tar.gz
cd src
make
make install
```
(d) Install 'tcsh'
If you do not have a copy of csh at '/usr/bin/csh' install 'tcsh':
```
sudo apt-get install tcsh (below requires it)
```
(e) Update the following paths in '~/DNCON2/metapsicov/runpsipredandsolv'
```
set dbname = /home/badri/DNCON2/databases/uniref/uniref90pfilt
set ncbidir = /home/badri/DNCON2/blast-2.2.26/bin
set execdir = /home/badri/DNCON2/psipred/bin/
set execdir2 = /home/badri/DNCON2/metapsicov/bin/
set datadir = /home/badri/DNCON2/psipred/data/ 
set datadir2 = /home/badri/DNCON2/metapsicov/data/
```

**[OPTIONAL] Verify 'runpsipredandsolv' installation:**  
```
cd ~/DNCON2/
cp ./metapsicov/examples/5ptpA.fasta ~/DNCON2/test-dncon2/
cd ~/DNCON2/test-dncon2/
../metapsicov/runpsipredandsolv 5ptpA.fasta
```
Check the expected output files '5ptpA.ss2', '5ptpA.horiz', and '5ptpA.solv'.

**(F) Install SCRATCH Suite** 
```
cd ~/DNCON2/
wget http://download.igb.uci.edu/SCRATCH-1D_1.1.tar.gz
tar zxvf SCRATCH-1D_1.1.tar.gz
cd SCRATCH-1D_1.1/
perl install.pl
// Replace the 32-bit blast with 64-bit version (if needed)
mv ./pkg/blast-2.2.26 ./pkg/blast-2.2.26.original
cp -r ~/blast-2.2.26 ./pkg/ (64-bit Legacy Blast is already installed)
```

**[OPTIONAL] Verify SCRATCH installation**  
```
cd ~/DNCON2/SCRATCH-1D_1.1/
cd doc
../bin/run_SCRATCH-1D_predictors.sh test.fasta test.out 4
```

**(G) Install HHblits and JackHMMER**  
```
sudo apt install hhsuite
```
```
cd ~/DNCON2/
wget http://eddylab.org/software/hmmer3/3.1b2/hmmer-3.1b2-linux-intel-x86_64.tar.gz
tar zxvf hmmer-3.1b2-linux-intel-x86_64.tar.gz
cd hmmer-3.1b2-linux-intel-x86_64
./configure
make
```
```
sudo apt-get install csh
```
```
cd ~/DNCON2/
wget ftp://ftp.ncbi.nih.gov/toolbox/ncbi_tools/ncbi.tar.gz
tar zxvf ncbi.tar.gz
csh
./ncbi/make/makedis.csh
exit
```

**(H) Configure DNCON2 scripts (in '~/DNCON2/DNCON2/scripts/' directory)**  

(a) Update the following variables in the script 'run-ccmpred-freecontact-psicov.pl'
```
FREECONTACT=> '/usr/bin/freecontact',
PSICOV    => '/home/badri/DNCON2/psicov/psicov',
CCMPRED   => '/home/badri/DNCON2/CCMpred/bin/ccmpred',
HOURLIMIT => 24,
NPROC     => 8
```

(b) Update the following variables in the script 'generate-alignments.pl' 
```
JACKHMMER   => '/home/badri/DNCON2/hmmer-3.1b2-linux-intel-x86_64/binaries/jackhmmer',
REFORMAT    => abs_path(dirname($0)).'/reformat.pl',
JACKHMMERDB => '/home/badri/DNCON2/databases/uniref/uniref90pfilt',
HHBLITS     => '/usr/bin/hhblits',
HHBLITSDB   => '/home/badri/DNCON2/databases/uniprot20_2016_02/uniprot20_2016_02',
CPU         => 2
```

(c) Update the following variables in the script 'dncon2-main.pl' 
```
SCRATCH      => '/home/badri/SCRATCH-1D_1.1/bin/run_SCRATCH-1D_predictors.sh',
BLASTPATH    => '/home/badri/ncbi-blast-2.2.25+/bin', 
BLASTNRDB    => '/home/badri/databases/nr90-2012',
PSIPRED      => '/home/badri/metapsicov/runpsipredandsolv',
ALNSTAT      => '/home/badri/metapsicov/bin/alnstats',
```

(d) Install NCBI Blast+ v2.2.25
```
cd ~/DNCON2/
wget ftp://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/2.2.25/ncbi-blast-2.2.25+-x64-linux.tar.gz
tar zxvf ncbi-blast-2.2.25+-x64-linux.tar.gz 
```

**(I)  Verify DNCON2 scripts**

(a) [OPTIONAL] Verify the script 'run-ccmpred-freecontact-psicov.pl'
```
cd ~/DNCON2/
./DNCON2/scripts/run-ccmpred-freecontact-psicov.pl ./DNCON2/dry-run/output/3e7u/alignments/3e7u.aln ./test-dncon2/temp-out-psicov ./test-dncon2/temp-out-ccmpred ./test-dncon2/temp-out-freecontact
```
Compare these outputs with the outputs at './DNCON2/dry-run/output/3e7u/'.

(b) [OPTIONAL] Verify the script 'generate-alignments.pl'
```
cd ~/DNCON2/
./DNCON2/scripts/generate-alignments.pl ./DNCON2/dry-run/input/T0900.fasta ./test-dncon2/temp-T0900-alignments/
```
Compare these outputs with the outputs at './DNCON2/dry-run/output/T0900/'.

(c) Verify DNCON2 installation by making contact predictions for three sequences - 3e7u, T0866, and T0900
```
cd ~/DNCON2/DNCON2/dry-run/
./run-3e7u.sh
./run-T0866.sh
./run-T0900.sh
```
Update the output paths in the script 'evaluate-runs.sh' execute it to evaluate precision of predicted contacts
```
./evaluate-runs.sh
```
Compare the evaluations with the outputs and logs at './DNCON2/dry-run/output' and './DNCON2/dry-run/results.txt'.

-----------------------------------------------------------
Badri Adhikari adhikarib@umsl.edu (developer)
Jianlin Cheng chengji@missouri.edu (PI)
