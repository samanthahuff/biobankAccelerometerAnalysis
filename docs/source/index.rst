.. accelerometer documentation master file, created by
   sphinx-quickstart on Tue Nov 27 12:48:46 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

.. image:: accelerometerLogo.png

A tool to extract meaningful health information from large accelerometer datasets. The software generates time-series and summary metrics useful for answering key questions such as how much time is spent in sleep, sedentary behaviour, or doing physical activity.

************
Installation
************

.. code-block:: console

    $ pip install accelerometer

You will also need Java 8 (1.8.0) or greater. Check with the following:

.. code-block:: console

    $ java -version

You can try the following to check that everything works properly:

.. code-block:: console

    # Create an isolated environment
    $ mkdir test_baa/ ; cd test_baa/
    $ python -m venv baa
    $ source baa/bin/activate

    # Install and test
    $ pip install accelerometer
    $ wget -P data/ http://gas.ndph.ox.ac.uk/aidend/accModels/sample.cwa.gz  # download a sample file
    $ accProcess data/sample.cwa.gz
    $ accPlot data/sample-timeSeries.csv.gz

***************
Getting started
***************
To extract a summary of movement (average sample vector magnitude) and
(non)wear time from raw Axivity .CWA (or gzipped .cwa.gz) accelerometer files:

.. code-block:: console

    $ accProcess data/sample.cwa.gz
    <output written to data/sample-outputSummary.json>
    <time series output written to data/sample-timeSeries.csv.gz>

The main output JSON will look like:

.. code-block:: console

    {
        file-name: "sample.cwa.gz",
        file-startTime: "2014-05-07 13:29:50",
        file-endTime: "2014-05-13 09:49:50",
        acc-overall-avg(mg): 32.78149,
        wearTime-overall(days): 5.8,
        nonWearTime-overall(days): 0.04,
        quality-goodWearTime: 1
    }

To visualise the time series and activity classification output:

.. code-block:: console

    $ accPlot data/sample-timeSeries.csv.gz
    <output plot written to data/sample-plot.png>

.. figure:: samplePlot.png

    Output plot of overall activity and class predictions for each 30sec time window

.. The underlying modules can also be called in custom python scripts:

.. .. code-block:: python

..     from accelerometer import summariseEpoch
..     summary = {}
..     epochData, labels = summariseEpoch.getActivitySummary("sample-epoch.csv.gz",
..             "sample-nonWear.csv.gz", summary)
..     # <nonWear file written to "sample-nonWear.csv.gz" and dict "summary" updated
..     # with outcomes>


***************
Citing our work
***************
When describing or using the UK Biobank accelerometer dataset, or using this tool
to extract overall activity from your accelerometer data, please cite [Doherty2017]_.

When using this tool to extract sleep duration and physical activity behaviours
from your accelerometer data, please cite [Willetts2018]_ [Doherty2018]_ and [Walmsley2020]_.

.. [Doherty2017] Doherty A, Jackson D, Hammerla N, et al. (2017) Large scale population assessment of physical activity using wrist worn accelerometers: the UK Biobank study. PLOS ONE. 12(2):e0169649

.. [Willetts2018] Willetts M, Hollowell S, Aslett L, Holmes C, Doherty A. (2018) Statistical machine learning of sleep and physical activity phenotypes from sensor data in 96,220 UK Biobank participants. Scientific Reports. 8(1):7961

.. [Doherty2018] Doherty A, Smith-Bryne K, Ferreira T, et al. (2018) GWAS identifies 14 loci for device-measured physical activity and sleep duration. Nature Communications. 9(1):5257

.. [Walmsley2020] Walmsley R, Chan S, et al. (2020) Reallocating time from machine-learned sleep, sedentary behaviour or light physical activity to moderate-to-vigorous physical activity is associated with lower cardiovascular disease risk (preprint https://doi.org/10.1101/2020.11.10.20227769)


*******
Licence
*******
This project is released under a `BSD 2-Clause Licence <http://opensource.org/licenses/BSD-2-Clause>`_ (see LICENCE file).


************
Contributors
************
See https://github.com/activityMonitoring/biobankAccelerometerAnalysis/graphs/contributors



.. toctree::
   :maxdepth: 1
   :caption: Contents:

   usage
   methods
   cliapi


******************
Indices and tables
******************

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
