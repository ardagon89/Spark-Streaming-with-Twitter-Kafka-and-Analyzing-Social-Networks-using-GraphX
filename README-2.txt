Part 1:
The Dataset is taken from the url https://snap.stanford.edu/data/web-Google.html. web pages and the hyperlinks are represented by  Nodes and directed edges respectively. Google released this data in 2002.

Implementation in Local System:
1. Open the project in IntelliJ.
2. Go to Run->Edit Configurations.
3. Create an application and GraphX. Select your jdk in first box, select GraphX in second box and type GraphX in Main class.
4. In the next box Specify the input file web-Google.txt as the first arguement and the location where you want to save output file as the second arguement.
5. Specify your Working directory and click apply.
6. Now go to Run->Run "GraphX".
7. After the code is executed the output files will be stored in the loation which was specified in the output arguement.



Implementation using aws:
1. Upload input file into S3 AWS Bucket after downloading it from the url "https://snap.stanford.edu/data/web-Google.html".
2. Upload the jar file in the same S3 bucket.
3. Create a cluster in the EMR. Start the cluster.
4. Add step to the cluster giving the path of the input file, jar file and to be created output files in the S3 bucket as parameters to the step.
5. The output path specified in the S3 bucket creates 5 different text files for each query output.
6. After adding the step, the cluster will start running. After it is completed, the output files can be downloaded from the S3 bucket.


1. Download the data web-Google.txt.gz from the "https://snap.stanford.edu/data/web-Google.html" . Upload the file to your S3 AWS Bucket.
This can also be retrieved from the public S3 bucket path - "https://assignment-3-bigdata.s3.amazonaws.com/web-Google.txt"
2. Create a S3 bucket in AWS account. Upload the text file to the S3 bucket.
3. Upload the jar file in the same S3 bucket.
4. Create a cluster in the EMR. Start the cluster.
5. Add step to the cluster giving the path of the input file, jar file and to be created output files in the S3 bucket as parameters to the step.
6. The output path specified in the S3 bucket creates 5 different text files for each query output.
7. After adding the step, the cluster will start running. After it is completed, the output files can be downloaded from the S3 bucket.
