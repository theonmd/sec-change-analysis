# Financial reports extraction and analysis

⚠️ WORK IN PROGRESS ⚠️

This project aims at extracting and analysing annual financial reports of publicly traded companies (10-K reports). The main idea comes from the [Lazy Prices paper](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=1658471) and states that changes in consecutive reports can be used as a proxy for the company's performance:

- High change is an indicator of low performance
- Low change is an indicator of high performance

This project was inspired by previous work from Osho Jha on [Sentiment Analysis on SEC reports](https://medium.com/@oshojha/useful-sentiment-analysis-mining-sec-filings-part-1-358942fc98ed).

## Implementation
### Data extraction

The first notebook 00_data_extraction.ipynb uses the [sec_edgar_downloader](https://sec-edgar-downloader.readthedocs.io/en/latest/) library to extract SEC filings from the Edgar database. It stores the raw files in a data folder. For example, to extract the last 5 10-K files from Tesla, run:

```
files_downloader("10-K", "TSLA", 5)
```


### Data processing

The second notebook 01_data_processing.ipynb processes the previously downloaded raw files to extract the relevant information and make them interpretable. To do so, we created the files_cleaning class that is composed of various processing and cleaning methods. To create the clean files from the previously downloaded Tesla files, run the following commands:

```
tsla_files = files_cleaning("TSLA")
tsla_files.write_clean_files()
```

### Change analysis

Once we have the financial reports extracted and processed, we can perform the proper change analysis and correlate it to the company's performance (*not done yet*). To do so, we use use a few comparison metrics:
- Euclidian distance
- Cosine similarity
- Jaccard similarity

And we combine these metrics with different ways of encoding the text (dtf, tf-idf...). To perform similarity analysis for the 2018 files compared to the other previous files, run the following command:

```
tsla_sim = similarities("TSLA")
tsla_sim.compute_sim("18", "jaccard", "dtf", "tfidf", "euclidian")
```

*Next steps: dive a bit more in change metrics, and rigorously link change to performance for a few selected companies*
