This project focuses on understanding mapreduce architecture on hadoop cluster(hadoop-3.3.0) using a real-word problem. We are implementing an unsupervized learning algorithm, "k-means algorithm" using MapReduce for a letter recognition task. 

Languages used: Python, Shell script

**Steps of the project:**

1) Generate test, train and initial centroids using sample.py file.

2) Implementing k-means algorithm as a mapper.py,reducer.py. Reducer.py returns updated centroids.Running the algorithm using updated centroids(from previous iteration) for 15 iterations using shell script.

3) Implementing u_reducer.py. Running mapper.py & u_reducer.py.

4) Assign the centroids for the test data(using new centroids) in c_mapper.py. Output only actual labels & predicted labels using c_reducer.py.

5) Using evaluation.py we evaluate the model by producing accuracy & heatmap of confusion matrix based on prediction.

**Workflow of this algorithm:**

![Image](https://github.com/sindhusheri97/KMeans_Map_Reduce/blob/main/workflow.png)

**File Description:**

**a) Sample.py:** Generates training and test data from the given data along with initial 26 initial cluster centroids. This program takes ‘n’ as input, where ‘n’ is the % of data to consider as training data

**b) Mapper.py:** Mapper function maps each data instance to its cluster label.

i) Read the training data and centroids

ii) For each training data instance, identify which cluster it belongs to. We can quantitatively define the data instance (x) membership in a cluster (c) using squared Euclidean distance: 

![Image](http://github.com/sindhusheri97/KMeans_Map_Reduce/blob/main/eucledian%20distance.png)

where d defines the number of features (16 in our case). Calculate Euclidean distance of x with all 26 centroids and choose the centroid label with minimum Euclidean distance

**c) Reducer.py:** The reducer function simply aggregates each data instance cluster membership and finds the updated cluster centroid coordinates.

i) Read the output of mapper.py

ii) Calculate new centroids by averaging features of all data instances in a cluster

iii) Output only the new cluster centroids

**d) u_reducer.py:** The u_reducer function simply aggregates each data instance cluster membership and finds the updated cluster centroids coordinates with updated labels.
i) Read the output of mapper.py

ii) Calculate new centroids by averaging features of all data instances in a cluster and assign centroid label as instance label which is highest in frequency

iii) Output only the new cluster centroids

**e) model.sh:** To iterate the k-means mapper & reducer for 15 times

i) Create a for-loop in the shell script that executes 15 times

ii) Within the for-loop have the MapReduce execution command to execute mapper.py and reducer.py

iii) The output of reducer.py must match the centroid input of the mapper.py to update cluster centroids in next iterations. But the MapReduce cannot write a file that already exists and it will throw FileAlreadyExists() exception

iv) To avoid the above problem, replace mapper’s centroid input with reducer’s output and delete the reducer output in model.sh

v) Outside the for-loop add the MapReduce execution command to execute mapper.py and u_reducer.py

**f) c_mapper.py:** To evaluate generated clusters by predicting labels of data instances given in test data

i) Read centroids generated by the k-Means clustering algorithm along with the test data

ii) Iterate over test data instances and obtain each instance’s corresponding cluster label by using the Euclidean distance using: 

![Image](http://github.com/sindhusheri97/KMeans_Map_Reduce/blob/main/eucledian%20distance.png)

This will be the predicted label

iii) Return only the actual data instance label and the predicted label

**g)c_reducer.py:** c_reducer.py simply combines c_mapper outputs.

i) Output actual label and the list of predicted labels in the following format: actual_label, predicted_label1, predicted_label2, predicted_label3, …, predicted_labelt

**h)evaluation.py:** This evaluates the predicted labels. This programs produces test accuracy(%) and a heatmap of confusion matrix based on prediction.

**i) letter-recognition.data:** Original data used for this project

**j) steps_to_execute_programs:** This file has steps to execute files on a hadoop cluster(hadoop-3.3.0)


