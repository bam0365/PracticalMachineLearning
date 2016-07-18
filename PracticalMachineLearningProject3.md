<h1>Practical Machine Learning</h1>


<h3>Introduction</h3>
<blockquote>
<p>The course project data is provided by:</p>




<p>Ugulino, W.; Cardador, D.; Vega, K.; Velloso, E.; Milidiu, R.; Fuks, H. [Wearable Computing: Accelerometers' Data Classification of Body Postures and Movements](http://groupware.les.inf.puc-rio.br/work.jsf?p1=10335). Proceedings of 21st Brazilian Symposium on Artificial Intelligence. Advances in Artificial Intelligence - SBIA 2012. In: Lecture Notes in Computer Science. , pp. 52-61. Curitiba, PR: Springer Berlin / Heidelberg, 2012. ISBN 978-3-642-34458-9. DOI: 10.1007/978-3-642-34459-6_6. </p>

<p>
<a href="http://groupware.les.inf.puc-rio.br/har">[Source:Human Activity Recognition](</a></p>


</blockquote>

<h3>Required R packages</h3>

<ul>
	<li>library(AppliedPredictiveModeling)</li>
	<li>library(caret)</li>
	<li>library(rattle)</li>
	<li>library(rpart.plot)</li>
	<li>library(ggplot2)</li>
	<li>library(randomForest)</li>
	<li>library(e1071)</li>
</ul>

<h3>Data Loading and Cleanup</h3>
<blockquote>
<p>trainingRaw <- read.csv(file="pml-training.csv", header=TRUE, as.is = TRUE, stringsAsFactors = FALSE, sep=',', na.strings=c('NA','','#DIV/0!'))
testingRaw <- read.csv(file="pml-testing.csv", header=TRUE, as.is = TRUE, stringsAsFactors = FALSE, sep=',', na.strings=c('NA','','#DIV/0!'))
</p>

<p>NAindex <- apply(trainingRaw,2,function(x) {sum(is.na(x))}) </p>
<p>trainingRaw <- trainingRaw[,which(NAindex == 0)]</p>
<p>NAindex <- apply(testingRaw,2,function(x) {sum(is.na(x))}) </p>
<p>testingRaw <- testingRaw[,which(NAindex == 0)]</p>

</blockquote>

<h3>Processing variables</h3>
<blockquote>

<p>v <- which(lapply(trainingRaw, class) %in% "numeric")</p>
<p>preObj <-preProcess(trainingRaw[,v],method=c('knnImpute', 'center', 'scale'))</p>
<p>trainLess1 <- predict(preObj, trainingRaw[,v])</p>
<p>trainLess1$classe <- trainingRaw$classe</p>
<p>testLess1 <-predict(preObj,testingRaw[,v])</p>

#Remove non-zero values
<p>nzv <- nearZeroVar(trainLess1,saveMetrics=TRUE)</p>
<p>trainLess1 <- trainLess1[,nzv$nzv==FALSE]</p>
<p>nzv <- nearZeroVar(testLess1,saveMetrics=TRUE)</p>
<p>testLess1 <- testLess1[,nzv$nzv==FALSE]</p>
</blockquote>

<h3>Create cross validation set</h3>
<blockquote>
<p>set.seed(12031987)</p>
<p>inTrain = createDataPartition(trainLess1$classe, p = 3/4, list=FALSE)</p>
<p>training = trainLess1[inTrain,]</p>
<p>crossValidation = trainLess1[-inTrain,]</p>
</blockquote>

<h3>Train Model</h3>
<blockquote>
<h4>Train model with random forest. Cross validation is used as train control method.</h4>
<p>modFit <- train(classe ~., method="rf", data=training, trControl=trainControl(method='cv'), number=5, allowParallel=TRUE )</p>

<h4>Training set:</h4>
<p>trainingPred <- predict(modFit, training)</p>
<p>confusionMatrix(trainingPred, training$classe)</p>

<h4>Confusion Matrix and Statistics</h4>
<p>cvPred <- predict(modFit, crossValidation)</p>
<p>confusionMatrix(cvPred, crossValidation$classe)</p>

</blockquote>
<h3>Result</h3>
<blockquote>
<p>testingPred <- predict(modFit, testLess1)</p>
<p>testingPred</p>
</blockquote>

