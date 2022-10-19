**In process Run_FastP**
error  Missing output file(s) `*qc.fq.gz` expected by process `Run_FastP (samplename)`

Solution in  fastp -i !{fastq_file[0]} -o !{samplename}.qc.gz -h !{samplename}_fastp.html --disable_adapter_trimming. Add 'fq'
Final result - fastp -i !{fastq_file[0]} -o !{samplename}.qc.fq.gz -h !{samplename}_fastp.html --disable_adapter_trimming

**In process fastq_hisat2_alignment_For_discovery**
error in -t !{hisat2 threads}. There is no such variable/

Solution change !{hisat2 threads} on 2 or random number.

**In process Predict_coding_abilities_by_CPAT**
error in output: file "novel.longRNA.CPAT.out"
 
Solution - add "ORF_prob.tsv". Final resoult need to look like this - "novel.longRNA.CPAT.out.ORF_prob.tsv"

**In process > Filter_lncRNA_by_coding_potential_result**
error in wrong columns. "awk '$4 >1&&$5=="lncRNA"{print $1}' novel.longRNA.txt" (on this step)

Solution - change $5 column on $2 column.

**In process > Summary_renaming_and_classification**
error with gtf format. 
"Error: no valid ID found for GFF record"

lines with transcript_id=="", they don't contain a valid ID, and so StringTie is complaining. 
Its always a bit of the worry to work out what to do with a the transcript_id field on gene lines in a GTF file. 
The orignal GTF format didn't contain gene lines, but they appear to have crept in at some point. 
The ENSEMBL files just don't have a transcript_id field on their gene lines, but i bet that trips StringTie up as well.

Solution - "awk '$3 != "gene" ' my_annotation.gtf > my_annotation_no_genes.gtf"

**process Build_kallisto_index_of_GTF_for_quantification**
error in index build. Error: repeated name in FASTA file

Solution - awk '/^>/{f=!d[$1];d[$1]=1}f' in.fa > out.fa before kallisto index build

**In Rerun_CPAT_to_evaluate_lncRNA**
error IndexError: list index out of range. Caused by empty row in 

Solution - in cpat.py change and add next lines:
`try:
		for l in open((options.out_file + '.ORF_prob.tsv'), 'r'):
			l = l.strip()
			print(l)
			if l.startswith('ID'):
				print ("seq_ID\t" + l, file=BEST)
				continue
			f = l.split('\t')
			seq_id = f[0].split('_ORF_')[0]
			prob = float(f[col_index])
			if seq_id not in best_candidates:
				best_candidates[seq_id] = f
			else:
				if prob > float(best_candidates[seq_id][col_index]):
					best_candidates[seq_id] = f
	except IndexError:
		pass`
  
**In Secondary_basic_statistic**
error in open file. Want "lncRNA.final.CPAT.out" but need "lncRNA.final.CPAT.out.ORF_prob.tsv"
 
 

