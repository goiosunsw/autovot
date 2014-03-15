autovot
=======
    
## Out of the box directories and files

    Path                        Description/contents
    autovot/                    Master directory
    auto_vot/                   
        bin/                    
        code/                   
            audiofile-0.3.4/    
            learning_tools/     
            Makefile            
            vot_predictor/
    makeConfigFiles.sh          Helper file to generate file lists
            
**There are two modes for training and decoding:**
* **Mode1:** The handling of feature extraction is hidden. The train can get cross-validation set or automatically select 20% of the training data.
* **Mode2:** Training and decoding is done after feature extraction. Features are extracted to a known directory once after which training and decoding may be done as needed. Mode 2 is ideal if you have a large quantity of data.

## User provided files and directories
***Important:*** Input files will be overwritten. If you wish to access your original files, be sure to back them up elsewhere.

#### Sound file format
* Wav files sampled at 16kHz

#### TextGrid file format
* Saved as text files with .TextGrid extension
* TextGrids for training must contain a tier with hand measured vot intervals. These intervals must have a common text label, such as "vot."
* TextGrids for testing must contain a tier with window intervals indicating the position the algorithm should begin looking for VOT onset. These intervals must have a common text label, such as "pos."

#### Directory format
User provided directories should be in the autovot master directory.

See example data & experiment folders.
    
    Path                        Description/Contents

    data/train/|                 Each embedded folder should contain both TextGrids and corresponding wav files
        voiced/
        voiceless/
    data/test/                  Each embedded voiced/voiceless folder should contain only TextGrids. 
                                Wav files should be in separate folder.
        voiced/
        voiceless/
        wav/
    
    experiments/config/:        Lists of filenames. See below for more info.
    experiments/models/:        Empty. This is where classifiers will be stored.
    experiments/temp_dir/:      Empty. This is where feature extraction will be stored in Mode 2.

#### Helper files for generating user's file lists
Move makeConfigFiles.sh to data/. Navigate to this folder and run:

    $ ./makeConfigFiles.sh
    
If successful, a .txt file will be generated for all voiced and voiceless TextGrids and wav files in each train and test directory
    
# Getting Started
## Compiling (TODO: fix layout)
### Compile Audiofile Library

    Navigate to auto_vot/code/audiofile-0.3.4/ and run:
        $ make
    If successful, the command line will print out a lot of text. The last line should be:
        make[2]: Nothing to be done for 'all-am'.

### Compile Code

    Navigate to auto_vot/code/ and run:
        $ make clean
    If successful, the output should be:
        [make] Cleaning completed
    Then, run:
        $ make
    Output should be:
        [make] Compiling completed
    

    Then navigate to experiments/ and run:
        $ export PATH=$PATH:*full path here*/autovot/auto_vot/bin
        
        
    TODO: Run:
        $ make install

# Usage
Tutorial to follow

## Training and feature extraction

#### Mode 1:
##### *Train AutoVOT*
###### Usage: auto\_vot\_train.py [OPTIONS] wav\_list textgrid\_list model_filename 

    Optional arguments                          Description
    
    -h                                          Dispaly help options
    --vot\_tier VOT_TIER                        name of the tier to extract VOTs from
    --vot\_mark VOT_MARK                        VOT mark value (e.g., "pos", "neg") or "*" for any
                                                string
    --window\_min WINDOW_MIN 
    --window\_max WINDOW_MAX
    --cv_auto
    --cv\_textgrid\_list CV\_TEXTGRID_LIST
    --cv\_wav\_list CV\_WAV_LIST
    --max\_num\_instances MAX\_NUM_INSTANCES
    --logging\_level LOGGING_LEVEL


    Positional Arguments                        Description
    
    wav_list                                    List of wav files
    textgrid_list                               List of TextGrids
    model_filename                              
                

#### Mode 2: 
##### *Extract acoustic features of AutoVOT*
###### Usage: auto\_vot\_extract\_features.py [OPTIONS] textgrid\_list wav\_list input\_filename features\_filename labels\_filename features_dir
    

    Positional Arguments                        Description
    
    textgrid_list                               list of manually labeled TextGrid files (input)
    wav_list                                    list of WAV files (input)
    input_filename                              AutoVOT front end input file name (output)
    features_filename                           AutoVOT front end features file name (output)
    labels_filename                             AutoVOT front end labels file name (output)
    features_dir                                directory to put the feature files (output)

    Optional Arguments:                         Description
    
    -h, --help                                  show this help message and exit
    --decoding                                  Extract features for decoding based on window_tier
                                                (vot_tier is ignored), otherwise extract features for
                                                training based on manual labeling given in the
                                                vot_tier
    --vot\_tier VOT_TIER                        name of the tier to extract VOTs from
    --vot\_mark VOT_MARK                        VOT mark value (e.g., "pos", "neg") or "*" for any
                                                string
    --window\_tier WINDOW_TIER                  used this window as a search window for training. If
                                                not given, a constant window with parameters
                                                [window\_min, window_max] around the manually labeled
                                                VOT will be used
    --window\_mark WINDOW_MARK                  window mark value or "*" for any string
    --window\_min WINDOW_MIN                    window left boundary (in msec) relative to the VOT
                                                right boundary (usually should be negative, that is,
                                                before the VOT right boundary.)
    --window\_max WINDOW_MAX                    window right boundary (in msec) relative to the VOT
                                                right boundary (usually should be positive, that is,
                                                after the VOT left boundary.)
    --max\_num\_instances MAX\_NUM_INSTANCES    max number of instances per file to use (default is to
                                                use everything)
    --logging\_level LOGGING_LEVEL              print out level (DEBUG, INFO, WARNING or ERROR)
                  

##### *Train AutoVOT after features extraction*
###### Usage: auto\_vot\_train\_after\_fe.py [OPTIONS] features\_filename labels\_filename model\_filename

    Positional Arguments:                       Description:
    features_filename                           AutoVOT front end features file name (training)
    labels_filename                             AutoVOT front end labels file name (training)
    model_filename                              output model file name

    Optional arguments:                         Description:
    -h, --help                                  show this help message and exit
    --logging\_level LOGGING_LEVEL              print out level (DEBUG, INFO, WARNING or ERROR)



