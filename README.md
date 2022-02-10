![Accelerometer data processing overview](docs/source/accelerometerLogo.png)

[![Github all releases](https://img.shields.io/github/release/activityMonitoring/biobankAccelerometerAnalysis.svg)](https://github.com/activityMonitoring/biobankAccelerometerAnalysis/releases/)
![install](https://github.com/activityMonitoring/biobankAccelerometerAnalysis/workflows/install/badge.svg)
![flake8](https://github.com/activityMonitoring/biobankAccelerometerAnalysis/workflows/flake8/badge.svg)
![junit](https://github.com/activityMonitoring/biobankAccelerometerAnalysis/workflows/junit/badge.svg)
![gt3x](https://github.com/activityMonitoring/biobankAccelerometerAnalysis/workflows/gt3x/badge.svg)
![cwa](https://github.com/activityMonitoring/biobankAccelerometerAnalysis/workflows/cwa/badge.svg)

A tool to extract meaningful health information from large accelerometer datasets. The software generates time-series and summary metrics useful for answering key questions such as how much time is spent in sleep, sedentary behaviour, or doing physical activity.

---
# INSTALLATION
## Create an isolated environment
```bash
$ /bin/bash
$ mkdir test_baa/ ; cd test_baa/
$ python -m venv baa
$ source baa/bin/activate
```

## Install and test
```bash
$ pip install accelerometer
$ wget -P data/ http://gas.ndph.ox.ac.uk/aidend/accModels/sample.cwa.gz  # download a sample file
$ accProcess data/sample.cwa.gz
$ accPlot data/sample-timeSeries.csv.gz
```
---

# DATA PROCESSING AND MODEL DEVELOPMENT

## Annotating IMU data with video

**In OMGUI:** 
- Export csv with time in seconds from OMGUI so that ELAN can read the tracks

**In terminal:**
Only for weeklong datasets that are too big to load in Elan
```bash
# Preprocessing of weeklong datasets for annotation
$ fullweek=$(cat dom_left_shank.csv | wc -l)
$ echo $fullweek
$ oneday=$((fullweek/7))                     
$ echo $oneday
$ head -n $oneday dom_left_shank.csv > dom_left_shank_cropped.csv
$ cat dom_left_shank_cropped.csv | wc -l  #check cropping worked
```

**In Elan:** 
1. (*optional*) Find rough (doesn't have to be exact) offset for video from beginning of filming to calibration (ex: 59890).
2. Go to drop jumps in video. Find first frame where foot has complete contact with the ground. 
3. Enter media synchronization mode. Select Player 1. *Apply current offsets*.
4. Select first file in drop down menu, then select Player 2. Find first peak of drop jumps. *Apply current offsets*.
5. Select next file in drop down menu to align next file. Select Player 2. Navigate to first peak of drop jumps. *Apply current offsets*. 
6. Continue with all four files so that first peaks of drop jumps are all aligned.
7. Make note of all of the offests. Manually set offsets so that they are file_offset(#4/5) - video_offset(#3) + beginning_offset(#1)

Make sure to: 
- annotate each second and only each second
- start first annotation, edit annotation time to start at 00:00:00.000.
    - if possible for last annotation, edit annotation time to be at the end of the xyz trace.
    - if video ends before xyz trace, than unannotated trace will get the final annotation
- export data from Elan as a .txt file with the following settings (add master media time offset/ include header lines/ exclude tier names /use both Begin and End time columns/ use hh:mm:ss.ms format)

**In MATLAB:**
1. Use offset from each file and run AX3_Annotate.m. 
2. Check annotated figures to make sure alignment occurred succesfully. 
3. An annotated .mat file will be saved

## Load virtual env
```bash
$ cd biobankAccelerometerAnalysis/accelerometer/
$/bin/bash
$ source ../test_baa/baa/bin/activate
```
## Process data file and extract features
```bash
accProcess ../../../data/mqir-souza1/Axivity/PHO/PHO_ax6_cwa_data/P009/P009.cwa --epochPeriod 1 --deleteIntermediateFiles 'False'
```
## Train model
```python
#open trainmodel.ipynb (redfines trainClassification model to fix bug in accClassification code, needs to use baa kernel)
trainClassificationModel( \
    "./activityModels/labelled-acc-epochs.csv", \
    featuresTxt="./accelerometer/activityModels/features_clipped.txt", \
    testParticipants="4,5", \
    outputPredict="./testpredictions2.csv", \
    rfTrees=1000, rfThreads=1)
```

---
## Usage
To extract a summary of movement from a raw Axivity accelerometer file (.cwa):

```bash
$ accProcess data/sample.cwa.gz

 <output written to data/sample-outputSummary.json>
 <time series output written to data/sample-timeSeries.csv.gz>
```

The main JSON output will look like:
```json
{
    "file-name": "sample.cwa.gz",
    "file-startTime": "2014-05-07 13:29:50",
    "file-endTime": "2014-05-13 09:49:50",
    "acc-overall-avg(mg)": 32.78149,
    "wearTime-overall(days)": 5.8,
    "nonWearTime-overall(days)": 0.04,
    "quality-goodWearTime": 1
}
```

To visualise the time series and activity classification output:
```bash
$ accPlot data/sample-timeSeries.csv.gz
 <output plot written to data/sample-timeSeries-plot.png>
```
![Time series plot](docs/source/samplePlot.png)

See the [documentation](https://biobankaccanalysis.readthedocs.io/en/latest/index.html) for more.

## Under the hood
Interpreted levels of physical activity can vary, as many approaches can be
taken to extract summary physical activity information from raw accelerometer
data. To minimise error and bias, our tool uses published methods to calibrate,
resample, and summarise the accelerometer data. 
<!-- [Click here for detailed information on the data processing methods on our wiki.](https://biobankaccanalysis.readthedocs.io/en/latest/methods.html) -->

![Accelerometer data processing overview](docs/source/accMethodsOverview.png)
![Activity classification](docs/source/accClassification.png)


## Citing our work
When describing or using the *UK Biobank accelerometer dataset*, please cite [Doherty2017].
When using *this tool* to extract sleep duration and physical activity behaviours from your accelerometer data, please cite:


1. [Doherty2017] Doherty A, Jackson D, et al. (2017)
Large scale population assessment of physical activity using wrist worn
accelerometers: the UK Biobank study. PLOS ONE. 12(2):e0169649

1. [Willetts2018] Willetts M, Hollowell S, et al. (2018)
Statistical machine learning of sleep and physical activity phenotypes from
sensor data in 96,220 UK Biobank participants. Scientific Reports. 8(1):7961

1. [Doherty2018] Doherty A, Smith-Byrne K, et al. (2018)
GWAS identifies 14 loci for device-measured physical activity and sleep
duration. Nature Communications. 9(1):5257

1. [Walmsley2021] Walmsley R, Chan S, Smith-Byrne K, et al. (2021)
Reallocation of time between device-measured movement behaviours and risk
of incident cardiovascular disease. British Journal of Sports Medicine.
Published Online First. DOI: 10.1136/bjsports-2021-104050

###### Licence
This project is released under a [BSD 2-Clause Licence](http://opensource.org/licenses/BSD-2-Clause) (see LICENCE file)
