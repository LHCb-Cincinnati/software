# How to Write and Test a Madgraph decay file in Gauss

### Writing the Decay File
I would recommend trying to build your decay file as the first step of this process.
An example decay file is [here](https://gitlab.cern.ch/lhcb-datapkg/Gen/DecFiles/-/blob/1f1d3abf81eaa51d9b35fe731e6768966888c26a/dkfiles/W_munumu=10GeV,MG.dec).
Try to follow all the conventions of the normal dec files: [link](https://gitlab.cern.ch/lhcb-datapkg/Gen/DecFiles/blob/master/CONTRIBUTING.md).

### Building Decfiles and Gauss
Next, we need to build the sim10 version of Gauss.  Follow the directions below, they have only been tested on lxplus.

```
lb-set-platform x86_64_v2-centos7-gcc11-opt
lb-set-workspace $PWD
mkdir Gen
cd Gen
git clone -b Sim10 ssh://git@gitlab.cern.ch:7999/lhcb-datapkg/Gen/DecFiles.git
cd DecFiles
ln -s . v32r999
```

Now, move whatever decay file you would like to create into the `dkfiles` folder.  Do not move it after building Decfiles, as it won't find the new deca file.
Ex: `cp test.dec dkfiles/test.dec`

```
make
cd ../../
lb-dev Gauss/v56r3
cd ./GaussDev_v56r3
make install
```

### Building MadgraphData
From inside of GaussDev, clone into the Madgraph Data repository by using the following command.
```
git clone ssh://git@gitlab.cern.ch:7999/lhcb-datapkg/Gen/MadgraphData.git
```

Next move into the directory and create a new branch for your new event type.
```
cd MadgraphData
git checkout -b ${USER}/<event type>
cd ..
./build.${CMTCONFIG}/run bash
```
The last command above will fail, but it is necessary.
Create the gridpacks with the following command, where 70000000.py is your new event number.
```
source MadgraphData/cmt/gridpack.sh $APPCONFIGOPTS/Gauss/Beam6500GeV-md100-2016-nu1.6.py ../Gen/DecFiles/options/70000000.py
```

