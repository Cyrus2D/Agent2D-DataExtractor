# Data Extractor for agents
In this repository, **Cyrus**  team have added C++ and Python scripts to **Agent2d-base** to extract data from agents in some specific situations.

This version of agent2d base is compatible with https://osdn.net/projects/rctools/downloads/51941/librcsc-4.1.0.tar.gz/

### How to install librcsc-4.1.0
```bash
wget https://osdn.net/projects/rctools/downloads/51941/librcsc-4.1.0.tar.gz/
tar -xzvf librcsc-4.1.0.tar.gz
cd librcsc-4.1.0
./configure
make
sudo make install
```

### How to compile the repository
In the first step you need to install librcsc-4.1.0, then download and extract the repository.
```bash
./bootstrap
./configure
cd src
make
```

### How to generate a dataset
In the first step you need to change the output directory in src/chain_action/DataExtractor.cpp line +191.
In the next step you should make the code and run it.

## DataExtractor.cpp/.h
In this class we have **update** function that is run every time chain action decide an action, and extract **features** and write them into files for each agent.
the file names determine agent unum and opponents team name and if parallel games run at time, it separate them by port of agents.

### Option
**DataExtractor** class  have a struct option that user can select easily which feature wants to write in the file for which team.

 ```c++
 DataExtractor::Option::Option() {
    side = BOTH;
    unum = BOTH;
    type = BOTH;
    body = BOTH;
    face = BOTH;
    tackling = BOTH;
    kicking = BOTH;
    card = BOTH;
    pos = BOTH;
    relativePos = BOTH;
    polarPos = BOTH;
    vel = BOTH;
    polarVel = BOTH;
    counts = BOTH;
    isKicker = BOTH;
    openAnglePass = TM;
    nearestOppDist = TM;
    polarGoalCenter = TM; // a polar vector from player to goal center
    openAngleGoal = TM;
    in_offside = TM;

    dribleAngle = Kicker;
    nDribleAngle = 12;

    worldMode = NONE_FULLSTATE;
    playerSortMode = X;

    use_convertor = true;
}
 ```

 ```c++ 
enum DataSide {
    NONE,
    TM,
    OPP,
    BOTH,
    Kicker
};
```

 Data side detemine that each data print for which teams.
 

 - None: none of teams
 - TM: just our team
 - OPP: just opponent team
 - BOTH: both of teams
 - Kicker: just Kickable player in our team

 
 #### dribleAngle  & nDribleAngle
 from player pos separate nDribleAngle zone and find nearest opponent in these zones, for example in angle range [30,60] dribleAngle=10, it means that in this range nearest opponent distance is 10.

 #### worldMode
 this feature determine from which world extract data, **world** or **fullstate_world**, in base you can change player.conf to separate these two.(in this project, player.conf have been changed for this purpose.)

#### playerSortMode
in this feature we determine the order of players to write it in file, which can be by their x of positions, or unums, or angles from kickable player. these are lambdas that used to sort the players.

```c++
enum PlayerSortMode {
    X,
    ANGLE,
    UNUM,
    RANDOM,
};
```

 - X: sort them by X position
 - Angle: sort them by angle from kicker to each player
 - UNUM: by their unum
 - Random: shuffle them

#### use_convertor
this feature determine that all data should be normalize or not.

## Python Scripts
in the script directory is python script tools

### 2dDataAnalysis
this class change the data base on inputs.
#### initialization
```python3
def __init__(self, file_in, file_out, sort_type=None, randomize=0, img_mode=False, normalize=True)
```

 - **file_in**: is input file of data.
 - **file_out**: is output file that result save in it.
 - **sort_type**: sort the order of players.
 - **randomize**: in every state(cycle) randomly select n pair of player in each team and swap their order.
 - **img_mode**: determine that save result files as png file. in this mode, **file_out** should be directory.
 - **normalize**: this input determine that data would be normalized or not.

### 2DOffense.py
this file make DNN by **Keras** and start learning.

### data_extractor_merger.py
this script base on input directory and an output file, merge all input directory files and merge them in output file.

### feature_importance.py
this script read an input csv file and determine feature importance for classifier outputs, and write in csv file file for each output.(the algorithm is Random Forest Classifier.)
