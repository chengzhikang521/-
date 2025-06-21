#!/bin/bash

# Directory containing input FASTQ files
DATA_DIR="data"
# Directory where SPAdes results will be stored
OUT_DIR="spades"
# SPAdes settings
THREADS=48
MEMORY=100

# Create output directory if it doesn't exist
mkdir -p "$OUT_DIR"

# Automatically find all paired samples in the data directory
for R1 in "$DATA_DIR"/*_1.fq.gz; do
  # Extract the sample name (e.g., pm1151 from pm1151_1.fq.gz)
  BASENAME=$(basename "$R1" _1.fq.gz)
  R2="$DATA_DIR/${BASENAME}_2.fq.gz"

  # Check if the paired file exists
  if [[ -f "$R2" ]]; then
    SAMPLE_OUT="$OUT_DIR/$BASENAME"
    echo "Assembling $BASENAME ..."
    spades.py -1 "$R1" -2 "$R2" -o "$SAMPLE_OUT" --isolate -t "$THREADS" -m "$MEMORY"
    echo "Finished $BASENAME. Results in $SAMPLE_OUT"
  else
    echo "Warning: Paired file for $R1 not found. Skipping."
  fi
done
# -
