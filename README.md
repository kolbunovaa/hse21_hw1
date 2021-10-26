### 1. Создаем место для 1-ого ДЗ:
```
mkdir hw
cd hw
mkdir 1
cd 1
```
### 2. Делаем ссылки на нужные файлы
```
ls -1 /usr/share/data-minor-bioinf/assembly/* | xargs -tI{} ln -s {}
```
### 3. Отбираем чтения
```
seqtk sample -s1026 oil_R1.fastq 5000000 > PE_R1.fastq
seqtk sample -s1026 oil_R2.fastq 5000000 > PEe_R2.fastq
seqtk sample -s1026 oilMP_S4_L001_R1_001.fastq 1500000 > MP_R1.fastq
seqtk sample -s1026 oilMP_S4_L001_R2_001.fastq 1500000 > MP_R2.fastq
```
### 4. Удаляем уже ненужные файлы
```
rm -r oil_R1.fastq
rm -r oil_R2.fastq
rm -r oilMP_S4_L001_R1_001.fastq
rm -r oilMP_S4_L001_R2_001.fastq
```
### 5. Используем fastQC и multiQC для оценки качества полученных чтений
```
mkdir fastqc
ls *.fastq | xargs -P 4 -tI{} fastqc -o fastqc {}

mkdir multiqc
multiqc -o multiqc fastqc
```
### 6. Подрезаем чтения по качеству, затем удаляем файлы, полученные seqtk
```
platanus_trim PE_R1.fastq PE_R2.fastq 
platanus_internal_trim MP_R1.fastq MP_R2.fastq

ls -1 *.fastq | xargs -tI{} rm -r {}
```
### 7. Оцениваем качество новых (подрезанных) чтений, используя опять же fastQC и multiQC
```
mkdir trimmed_fastq
mv -v *trimmed trimmed_fastq/
mkdir trimmed_fastqc
ls trimmed_fastq/* | xargs -P 4 -tI{} fastqc -o trimmed_fastqc {}

mkdir trimmed_multiqc
multiqc -o trimmed_multiqc trimmed_fastqc
```
### 8. Собираем контиги и скаффолды
```
time platanus assemble -o Poil -f trimmed_fastq/PE_R1.fastq.trimmed trimmed_fastq/PE_R2.fastq.trimmed 2> assemble.log

time platanus scaffold -o Poil -c Poil_contig.fa -IP1 trimmed_fastq/PE_R1.fastq.trimmed  trimmed_fastq/PE_R2.fastq.trimmed -OP2 trimmed_fastq/MP_R1.fastq.int_trimmed trimmed_fastq/MP_R2.fastq.int_trimmed 2> scaffold.log
```
## До



