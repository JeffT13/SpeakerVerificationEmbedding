# LibriSpeech Speaker Verification

#### HPC Prerequisities

All the modules in HPC needed for this process are:

`module purge`

`module load python3/intel/3.7.3` 

`conda install -c auto pydub`

`module swap anaconda3  python/intel/2.7.12`

`module load librosa/intel/0.5.0`

## Speaker Verification Model Training Set

### Details
Using LibriSpeech2 dataset for evaluating diarization model (https://github.com/EMRAI/emrai-synthetic-diarization-corpus) . 
2-person dialogues without overlap were used in training the Speaker Verification Model. Audio and diarization was used from the subfolder: https://github.com/EMRAI/emrai-synthetic-diarization-corpus/tree/master/librispeech2/train_clean_100

#### Downloading LibriSpeech2

Most of our computing is done on an HPC that does not support the requests package and therefore downloading the LibriSpeech2 data was done locally, and files are transferred to the HPC cluster manually.

The specific folder containing the test set was downloaded using sparse checkout. This can be done using the following steps dervied from https://stackoverflow.com/questions/33066582/how-to-download-a-folder-from-github?noredirect=1&lq=1:

1.  Create a directory

    mkdir librispeech
    
    cd librispeech
    
2.  Set up a git repo

    git init
    
    git remote add origin https://github.com/EMRAI/emrai-synthetic-diarization-corpus

3.  Configure git-repo to download only specific directories

    git config core.sparseCheckout true

4.  Set the folder to the test_clean folder (do this step separately for both audio files and ctms (diarizations)

    echo "librispeech2/train_clean_100/wavs" > .git/info/sparse-checkout 
    
    echo "librispeech2/train_clean_100/ctms" > .git/info/sparse-checkout

5.  Download repo

    git pull origin master
    
### Pre-Processing Instructions

1.  Place downloded LibriSpeech2 audio .wav files in `./librispeech/audio` folder on HPC. 

2.  Place downloaded LibriSpeech2 diarized .ctm files in `./librispeech/diarization` folder on HPC.

3.  Place all scripts from these instructions in `./` folder (the current working directory).

4.  Run `rename_audiofiles.py` and `rename_ctmfiles.py` to remove periods within the file names.

5.  Run `libri_train_merged.py` to create `speakers` folders for all speakers within the train set. Each speaker subfolder will contain files of audio segments where [start_time end_time] is saved as .txt file, and corresponding .wav files are also saved. The directory will look like this: `./librispeech/speakers/speaker_id` and will contain audio segment files and text files.

6.  Run `del_small_train_libri.py` to remove .wav files that are <40KB, as well as with their corresponding .txt files. These audio files are too small to process; buffer is too short and it is likely one or less words spoken which will not be picked up by VAD.

7.  Place config folder containing config_Libri.yaml in `./librispeech/`.

8.  Run data_preprocess.py from TIMIT folder in git repo to create train_tisv and test_tisv folders. Train/test will automatically split 90/10.

## UIS-RNN Evaluation Set

### Details
Using LibriSpeech3 dataset for evaluating diarization model (https://github.com/EMRAI/emrai-synthetic-diarization-corpus) . 
3-person dialogues without overlap were used in the evaluation since it best represented the SCOTUS dialogues (a few speakers throughout with no overlap). Audio and diarization was used from the subfolder: https://github.com/EMRAI/emrai-synthetic-diarization-corpus/tree/master/librispeech3/test_clean

### Prerequisites 

#### Downloading LibriSpeech3

Most of our computing is done on an HPC that does not support the requests package and therefore downloading the LibriSpeech3 data was done locally, and files are transferred to the HPC cluster manually.

The specific folder containing the test set was downloaded using sparse checkout. This can be done using the following steps dervied from https://stackoverflow.com/questions/33066582/how-to-download-a-folder-from-github?noredirect=1&lq=1:

1.  Create a directory

    mkdir librispeech
    
    cd librispeech
    
2.  Set up a git repo

    git init
    
    git remote add origin https://github.com/EMRAI/emrai-synthetic-diarization-corpus

3.  Configure git-repo to download only specific directories

    git config core.sparseCheckout true

4.  Set the folder to the test_clean folder (do this step separately for both audio files and ctms (diarizations)

    echo "librispeech3/test_clean/wavs" > .git/info/sparse-checkout 
    
    echo "librispeech3/test_clean/ctms" > .git/info/sparse-checkout

5.  Download repo

    git pull origin master

### Pre-Processing Instructions

1.  Place downloded LibriSpeech3 audio .wav files in `./librispeech/audio` folder on HPC. 

2.  Place downloaded LibriSpeech3 diarized .ctm files in `./librispeech/diarization` folder on HPC.

3.  Place all scripts from these instructions in `./` folder (the current working directory).

4.  Run `rename_audiofiles.py` and `rename_ctmfiles.py` to remove periods within the file names.

5.  Run `libri_eval_merged.py` to create `books` folders for each book in the test set. Each book in the subfolder will store speakers as folders. These speaker folders will contain files of audio segments where [start_time end_time] is saved as .txt file, and corresponding .wav files are also saved. The directory will look like this: `./librispeech/books/Book_X/speaker_id` and will contain audio segment files and text files.

6.  Run `del_small_eval_libri.py` to remove .wav files that are <40KB, as well as with their corresponding .txt files. These audio files are too small to process; buffer is too short and it is likely one or less words spoken which will not be picked up by VAD.


## D-vector embedding
>>>>>>> refac:Libri/README.md

