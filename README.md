# Financial reports extraction and analysis

This project aims at extracting and analysing annual financial reports of publicly traded companies (10-K reports). The main idea comes from the [Lazy Prices paper](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=1658471) and states that changes in consecutive reports can be used as a proxy for the company's performance:

- High change is an indicator of low performance
- Low change is an indicator of high performance

This project was inspired by previous work from Osho Jha on [Sentiment Analysis on SEC reports](https://medium.com/@oshojha/useful-sentiment-analysis-mining-sec-filings-part-1-358942fc98ed).

## Implementation
### Data extraction

The first notebook A_data_extraction.ipynb uses the [sec_edgar_downloader](https://sec-edgar-downloader.readthedocs.io/en/latest/) library to extract SEC filings from the Edgar database. It stores the raw files in a data folder. For example, to extract the last 5 10-K files from Tesla, run:

```
files_downloader("10-K", "TSLA", 5)
```


### Data processing

The second notebook I_data_processing.ipynb processes the previously downloaded raw files to extract the relevant information and make them interpretable. To do so, we created the files_cleaning class that is composed of various processing and cleaning methods. To create the clean files from the previously downloaded Tesla files, run the following commands:

```
tsla_files = files_cleaning("TSLA")
tsla_files.write_clean_files()
```

### Change analysis

The code for change analysis can be found in the II_models.ipynb notebook. Once we have the financial reports extracted and processed, we can perform the proper change analysis and correlate it to the company's performance. To do so, we use use a few comparison metrics:
- Euclidian distance
- Cosine similarity
- Jaccard similarity

And we combine these metrics with different ways of encoding the text (dtf, tf-idf...). To perform similarity analysis for the 2018 files compared to the other previous files, run the following command:

```
tsla_sim = similarities("TSLA")
tsla_sim.compute_sim(18, "jaccard", "dtf", "tfidf", "euclidian")
```

### Automation of previous steps

In the III_analysis.ipynb notebook, you will find functions that automate the execution of the previous steps (files extraction, text processing, change analysis). We selected a limited number of companies to analyze, stored in the stock_tickers.csv file (main large caps in the US stock market). To clean you data folder and remove all previously downloaded files, run: 

```
clean_all()
```

To run all the previous steps in one batch on the p first companies of the stock_tickers.csv for the past 20 years of filings, run:

```
comparisons(tickers[1:p], n = 20)
```

This will extract all required data, process it, compute comparisons, and store the results in a pickle file named similarities.pickle and stored in the data folder (and each company's similarity results will also be stored independently in every company's folder.

### Optimal change threshold

The code for this step can be found in the IV_gaussian_analysis.ipynb notebook. In order to find the optimal threshold to separate change and no-change years for each company, we decided to plot every company's distribution of similarities and separate it in 2 groups using a Gaussian Mixture with 2 components.

To do so, load the previously created pickle file as sim and run:

```
gauss_ana = gaussian_analysis(sim) # create the gaussian analysis object
gauss_ana.plot_gaussians() # plot the fitted Gaussian ditributions
gauss_ana.threshold_estimation() # compute the threshold estimations 
```

The thresholds are stored in a dictionnary with tickers as keys and thresholds as values.

### Financial analysis

The code for this final step can be found in the V_financial_performance.ipynb notebook. This final parts brings back the analysis to the initial objective, which was to link the change (or no change) of financial reports to companies' performance. To do so, load the previously created similarities and threshold dictionnaries and run the following code:


```
pf = performance(sim, thresh)
pf.evaluate_portfolio()
```

This work was produced by a group of UC Berkeley students for their capstone project. If you want more information, don't hesitate to contact me!
