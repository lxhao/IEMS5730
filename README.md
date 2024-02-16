# IEMS5730
IEMS5730 Spring 2024 Homework #1
Community Detection in Online Social Networks

本次作业主要是hadoop streaming 的使用，一种可行的解决思路：
1. 对输入的原始数据，mapper中用follower 作为key，在reducer中聚合得到每个follower所关注的所有followee。
2. 对每个follower的所有followee两两组合后，得到需要计算相似对的候选对
3. 对候选对做个去重，避免重复计算，得到中间结果1，后续每个任务都会用到这个数据。
4. 对原始输入数据重新来一轮计算，mapper中用follower 作为key，在reducer中聚合得到每个follower所关注的所有followee，得到中间结果2，后续每个任务也都会用到这个数据。
5. 对结果1做一轮mr计算，运行前把结果2的文件，分发到每个task，运行reducer任务时先并把这个文件加载到内存，以follower作为key,关注的所有followee集合作为value，然后对结果1中的每个候选对，计算共同的followee集合和相似对

支持作业、考试辅导、代写，具体可以加微信lxhao580，老师直接接单，不经过中介平台，价格优惠，服务靠谱

Problem Description:
Community detection has drawn lots of attention these years. With the popularity of online
social networks, we can acquire many valuable datasets to develop community detection
algorithms. In this homework, you will implement a community detection algorithm across
three datasets for blogs.
Blogs or microblogs are typical applications of online social networks. Basically, blogs with
higher similarities are more inclined to belong to the same community. In this homework, we
measure the similarity of blogs by the number of common links they share on their pages.
There are two types of relationships in a blog network. One is symmetric, i.e., blog X has a
link to blog Y, and blog Y also provides a link directing back to blog X; the other is
asymmetric, which means blog Y does not link to blog X even though blog X has a link to
blog Y. In the latter case, there are two roles involved in this relationship: follower and
followee (like the case in Twitter or Weibo). When blog X has a link to Y, X is the follower,
and Y is the followee.
To detect communities, we need to calculate the similarity between any pair of blogs. In
this homework, for a given pair of blogs, the similarity is measured by the number of
common followees divided by the total (deduplicated) number of the two blogs’ followees.
Specifically, the formal definition of similarity is as follows:
If >0, we define
··································(*)
where out(A) is the set of all followees of blog A, and |S| is the cardinality of S.
If , we set the similarity to 0.
Motivating Example:
The following figure illustrates the process of calculating the (pair-wise) similarity. Five
blogs, A, B, C, D, and E, are involved in this example.
The set of followees of A is {B, C, E}, and the set of followees of B is {A, C, E}. There are 2
common followees between A and B (i.e., C and E), and the number of the union of their
followees is 4 (A, B, C, E). The similarity between A and B is 2/4 = 0.5.
Dataset:
We provide three datasets with different sizes. The small dataset contains around 4K blogs;
the medium one contains around 80K blogs and the large one with over 100K blogs. Each
blog is represented by its unique ID number. The download links of all datasets are listed in
reference [1]-[3]. The small dataset is provided to facilitate your initial debugging and testing.
The design of your program should be scalable enough to handle all the datasets.
Sample Input:
The format of the data file in the above example is as follows:
A B
A C
A E
B A
B C
B E
C E
D A
E C
Sample Output:
We anticipate the community detection results to identify the TOP K most similar blogs for
EACH individual blog. An example of the output format (K=3) can be as follows:
A: B C E
B: A D E
C: A B
D: B
E: A B
Note: For each individual blog,
1. different similar blogs are separated by space;
2. retain all blog IDs with identical similarities, but in the event of a tie, some may be
randomly abandoned to ensure the total count does not exceed K;
3. blogs with 0 similarity are omitted.
Objective:
Solve the above community detection problem using MapReduce. Four tasks (plus one bonus
task) with instructions can be found below. Note that the output format of each specific task
may vary.
[Task Requirements]
1. You can either use the IE DIC or the Hadoop cluster you built for HW#0 to run your
MapReduce program(s).
2. You are free to use any programming languages (e.g., Java [4], Python [5] or C/C++)
to implement the required MapReduce components, including mapper(s), reducer(s),
etc. (e.g. by leveraging the “Hadoop Streaming” capability [6]).
3. Again, the design of your program should be scalable enough to handle all three
datasets.
[Submission Requirements]
1. Submit the source codes and outputs of your programs in one single PDF report.
Besides, for each key step, you should also present the commands used (if
applicable), the descriptions (in pure texts), and illustrations (in figures/ screenshots)
that help convey and clarify your ideas for solving the problems. In particular, for
each Hadoop job (running a map-reduce function pair), please give a brief
description to explain its functionality.
2. Package all the source codes (as you included in Step 1) into a zip file. Please
submit both the PDF report and the zip file to CUHK Blackboard separately.
3. As for tasks (a), (b) and (e) below, submit the community detection results of all those
blogs whose IDs share the same last 4 digits with your CUHK student ID. For
example, if your student ID is 1155004321, then you need to submit the results for
blogs with ID = 4321, 14321, 24321, 34321, …, 114321, …
Note that this requirement only serves as a filter for the original outputs. In other
words, you still need to run your MapReduce jobs on every blog and harvest all
outputs. This extra step is to trim the raw outputs (only picking certain rows according
to your CUHK ID) for homework report purposes.
Tasks.
a. [25 marks] For EVERY blog, recommend the blog with the maximal number of
common followees in the medium-sized dataset [2]. If multiple blogs share the same number,
pick the one with the smallest ID. Your output should consist of m lines, where m is the total
number of blogs. Each line follows the format below:
A:B, {C,E}, 2
where “A:B” is the blog pair, “{C,E}” is the set of their common followees, “2” is the count of
common followees.
Note:
1. For the set of common followees, there is no special requirement for the elements’
sequence, i.e., both {C,E} and {E,C} are acceptable. The same applies to Q1(b) and
Q1(e).
2. If a blog pair does not have common followees, you are allowed to omit this pair in
the output.
3. To determine the “smallest” ID when there is a tie, IDs are compared as integers
rather than strings. E.g., in most programming languages, 987 < 1987 but “987” >
“1987”. We assume 987 is the smaller ID.
b. [30 marks] Find the TOP K (K=3) most similar blogs of EVERY blog as well as their
common followees for the medium-sized dataset [2]. If multiple blogs have the same
similarity with a particular blog, they should all be included in your results. (Still, the total
number of records for each blog should not exceed K, you may choose which results to
keep/ abandon arbitrarily when there’s a tie). For each pair of blogs, output a line with the
following format:
A:B, {C,E}, simscore
where “simscore” is the similarity score between A and B.
Hints:
1. To facilitate the computation of the similarity as defined in Formula (*) in the Problem
Description, you can use the inclusion-exclusion principle .
2. You are allowed to use the “sort” command on Linux to get the top K similar blogs
after you have computed similarity scores for all pairs.
c. [25 marks] In fact, each blog is annotated with a label indicating its community. In
each dataset, a label file is provided, with the first column indicating the blog ID and the
second column indicating the label value. For example, the small dataset has seven different
labels (the value ranges from 0 to 6), which means that each blog is from one of the seven
communities.
For each community in the medium dataset, please figure out how many (unique) members
act as the common followees of other blogs. (For example, suppose that A, B, C, D, E are
labeled with community 0, 1, 2, 1, 2, respectively. Then, for community 0, one of its members
(blog A) acts as the common followee of others (blog B and D). As for community 1, none of
its members is the common followee of others.) Your reported results should be formatted
like the following example:
Community 0: 1
Community 1: 0
Community 2: 2
d. [20 marks] Run part (a) for the medium dataset multiple times while modifying the
number of mappers and reducers for your MapReduce job(s) each time. You need to
examine and report the performance of your program for at least 4 different runs. Each run
should use a different number of mappers and reducers. Note that the number of mappers or
reducers should not be less than 2.
For each run, performance statistics to be reported should include: (i) the time consumed by
the entire MapReduce job(s); (ii) the maximum, minimum and average time consumed by
the mapper and reducer tasks; (iii) tabulate the time consumption for each MapReduce job
and its tasks. (One example is given in the following table.) Moreover, describe (and explain,
if possible) your observations.
Example:
1st Run:
#Job Mapper
num
Reducer
num
Max
mappe
r time
Min
mappe
r time
Avg
mappe
r time
Max
reduce
r time
Min
reduce
r time
Avg
reduce
r
time
Total
time
1 3 2 60s 40s 50s 60s 40s 50s 2.5 min
2 … … … … … … … … …
Note:
1. The number of rows in the table depends on the number of jobs (one distinct map
and reduce function for each job) you chain to complete part (a).
2. The elapsed time for each Map/Reduce Task can be found on Job History Service
Web UI (port 19888).
For students using their own VMs, start the service by running
./sbin/mr-jobhistory-daemon.sh start historyserver. Remember to set up
proper firewall rules/ use ssh port forwarding so as to access the Web UI on your
local browser.
For students using IE DIC Cluster, the Web UI is served at
http://dicvmd2.ie.cuhk.edu.hk:19888/. Moreover, users can find the details of a
particular job via e.g.
http://dicvmd2.ie.cuhk.edu.hk:19888/jobhistory/job/job_1694578679658_0003, where
job_1694578679658_0003 is the ID of the job you created.
e. [Bonus 20 marks*] Find the TOP K (K=4) most similar blogs and the list of common
followees for each blog in the large dataset in [3] using the format of Q1(b). (Hints: To
reduce the memory consumption of your program, you may consider using the composite
key design pattern and secondary sorting techniques, as discussed in [7] and [8].)
General Hints:
1. Going through HW#0 Q1(c), (d)’s source codes may help you understand how to
construct your mapper & reducer code here. However, unlike WordCount, you cannot
always expect to finish all your work with only one single mapper and reducer job.
Chaining multiple (i.e. a series of different) MapReduce jobs to handle complex
community detection problems is a standard approach. It may be difficult to use just
one MapReduce job to get the final results for each problem due to memory
exhaustion and the parallel & distributed nature of data in Mappers/Reducers.
2. For each dataset, there are two files included. The one with the suffix ‘_relation’
indicates the mutual relations of each pair of blogs. The one with the suffix ‘_label’
indicates the community label for each blog.
3. For students who did not manage to set up their Hadoop cluster in HW#0, please
contact the TAs. You can either choose to set up the cluster with TAs’ help or you can
run your MapReduce programs on the IE DIC Cluster, where Hadoop has already
been installed. Once the IE DIC cluster is ready, TAs will inform you of the account
information. More details will be provided in the tutorial/ on the CUHK blackboard.
4. If you use Java, you can specify the number of mappers with the following code:
job.setNumMapTasks(20). If you use Hadoop streaming with Python, you can specify
it via the following command option: -D mapred.map.tasks=20. The number of
reducers can be modified similarly.
If this does not work, you may need to modify the split size in
$hadoop/etc/hadoop/mapred-site.xml: mapred.min.split.size=268435456. Refer to
[9] for more information.
5. As for the large dataset, you may want to set mapreduce.map.output.compress=true
to compress the intermediate results, in case you don’t have enough local hard disk
space (to hold the intermediate tuples).
6. Tackling the problems may take much longer than you expect. Particularly, the large
dataset may take a long time to process, even if everything is correct. Please start
doing this assignment as early as possible.
References:
[1] Small-scale dataset
http://mobitec.ie.cuhk.edu.hk/iems5730Spring2024/static_files/homework/small.tar.gz
[2] Medium-scale dataset
http://mobitec.ie.cuhk.edu.hk/iems5730Spring2024/static_files/homework/medium.tar.gz
[3] Large-scale dataset
http://mobitec.ie.cuhk.edu.hk/iems5730Spring2024/static_files/homework/large.tar.gz
[4] Write a Hadoop program in Java
https://hadoop.apache.org/docs/stable/hadoop-mapreduce-client/hadoop-mapreduce-client-c
ore/MapReduceTutorial.html
[5] Write a Hadoop program in Python2
http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/
[6] Hadoop Streaming
https://hadoop.apache.org/docs/r2.9.2/hadoop-streaming/HadoopStreaming.html
[7] Composite Key
https://techmytalk.com/2014/11/14/mapreduce-composite-key-operation-part2/
[8] Secondary Sort
http://codingjunkie.net/secondary-sort/
[9] How many Mappers and Reducers?
https://cwiki.apache.org/confluence/display/HADOOP2/HowManyMapsAndReduces
