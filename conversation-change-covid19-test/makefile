# Notes:
# - If run on unix system, use rm instead of del command in clean
# - Careful with spaces! If use \ to split to multiple lines, cannot have a space after \

# OVERALL BUILD RULES

all: scrape_data data_cleaned data_merged results paper
scrape_data: data/dataset_eredivisiemax100.csv
data_cleaned: gen/data-preparation/output/tdm_pre.csv gen/data-preparation/output/tdm_post.csv gen/data-preparation/output/scores_pre.RData gen/data-preparation/output/scores_post.RData
data_merged: gen/data-preparation/output/merged_sentiment.csv
results: gen/analysis/output/analysis_results.RData
paper: gen/paper/output/paper.pdf
.PHONY: clean

# INDIVIDUAL RECIPES

# Generate paper/text
gen/paper/output/paper.pdf: gen/paper/output/table1.tex \
				src/paper/paper.tex
	pdflatex -interaction=batchmode -output-directory='gen/paper/output/' 'src/paper/paper.tex'
	pdflatex -interaction=batchmode -output-directory='gen/paper/output/' 'src/paper/paper.tex'
	pdflatex -output-directory='gen/paper/output/' 'src/paper/paper.tex'
# Note: runs pdflatex multiple times to have correct cross-references

# Generate tables
gen/paper/output/table1.tex: gen/analysis/output/analysis_results.RData \
	Rscript src/paper/tables.R

# Run analysis
gen/analysis/output/analysis_results.RData: gen/data-preparation/output/merged_sentiment.csv \
						src/analysis/analyze.R \
						src/analysis/update_input.R
	Rscript src/analysis/update_input.R
	Rscript src/analysis/analyze.R

# Merge data
gen/data-preparation/output/merged_sentiment.csv: gen/data-preparation/output/scores_pre.RData \
						gen/data-preparation/output/scores_post.RData \
						src/data-preparation/merge_data.R
	Rscript src/data-preparation/merge_data.R

# Clean data
gen/data-preparation/output/tdm_pre.csv gen/data-preparation/output/tdm_post.csv gen/data-preparation/output/scores_pre.RData gen/data-preparation/output/scores_post.RData:data/dataset_eredivisiemax100.csv \
						src/data-preparation/clean_data.R \
						src/data-preparation/update_input.R
	Rscript src/data-preparation/update_input.R
	Rscript src/data-preparation/clean_data.R

# Scrape data
data/dataset_eredivisiemax100.csv: src/data-preparation/scraper_team5.py
	python scraper_team5.py

# Create gen structure
gen gen/analysis gen/data-preparation gen/paper gen/analysis/input gen/analysis/output gen/analysis/temp gen/data-preparation/input gen/data-preparation/output gen/data-preparation/temp gen/paper/input gen/paper/output gen/paper/temp: src/data-preparation/create_gen.R
	Rscript src/data-preparation/create_gen.R

# Clean-up: Deletes temporary files
# Note: Using R to delete files keeps platform-independence.
# 	    --vanilla option prevents from storing .RData output
clean:
	Rscript --vanilla src/clean-up.R
