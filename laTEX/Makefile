# Basic Configuration.
TEXFILE = main.tex
FILE_NAME = $(basename $(TEXFILE))
OUTPUT_DIR = build
BIBFILE = references.bib

# Tex Live Commands.
LATEX = pdflatex
BIBER = bibex
LATEX_ARGS = -interaction=nonstopmode -output-directory=$(OUTPUT_DIR)

# Phony targets. (They don't check for created files themselves)
.PHONY: all clean view refresh bib cleanup live status kill

# Default target: build the PDF and clean up.
all: $(OUTPUT_DIR)/$(FILE_NAME).pdf cleanup

# Create output directory just if it's not already created.
# Compile the LaTeX document to PDF.
$(OUTPUT_DIR)/$(FILE_NAME).pdf: $(TEXFILE) | $(OUTPUT_DIR)/
	$(LATEX) $(LATEX_ARGS) $(TEXFILE)
	-$(MAKE) bib
	$(LATEX) $(LATEX_ARGS) $(TEXFILE)
	$(LATEX) $(LATEX_ARGS) $(TEXFILE)

$(OUTPUT_DIR)/:
	mkdir -p $(OUTPUT_DIR)

$(OUTPUT_DIR): $(OUTPUT_DIR)/$(FILE_NAME).pdf

# Run Bibtex alone
bib: | $(OUTPUT_DIR)
	$(BIBER) --output-directory=$(OUTPUT_DIR) $(FILE_NAME)

# Keep only the PDF in the build directory, remove all other files.
cleanup:
	@echo "Cleaning up: Keeping only the PDF file in build directory..."
	@find $(OUTPUT_DIR) -type f -not -name "$(FILE_NAME).pdf" -delete

# Clean up all files including the PDF.
clean:
	rm -rf $(OUTPUT_DIR)/*

# View the PDF (requires a PDF viewer; select the one)
view: $(OUTPUT_DIR)/$(FILE_NAME).pdf
	@if command -v xdg-open > /dev/null; then \
		xdg-open $<; \
	elif command -v open > /dev/null; then \
		open $<; \
	else \
		echo "Could not find a suitable PDF viewer command."; \
	fi

# Continuous Compilation in background (proccess in the back)
background:
	@echo "Starting latexmk in background..."
	@latexmk -pdf -pvc -interaction=nonstopmode -output-directory=$(OUTPUT_DIR) $(TEXFILE) > latex.log 2>&1 &
	@echo "latexmk running in background. Output at latex.log."
	@echo "Use 'pkill latexmk' to stop or 'make kill'."

# Continuopus Compilation in foreground (current terminal)
live:
	@echo "Starting latexmk.."
	@latexmk -pdf -pvc -f -interaction=nonstopmode -output-directory=$(OUTPUT_DIR) $(TEXFILE)

status:
	@ps aux | grep "latexmk -pdf -pvc" | grep -v grep > /dev/null && echo "latexmk process is running" || echo "latexmk process is not running"

kill:
	@echo "Killing latexmk processes..."
	@pkill -f "latexmk.*main\.tex$$" 2>/dev/null || echo "No latexmk processes found"

# Full refresh, clean then build.
refresh: clean all