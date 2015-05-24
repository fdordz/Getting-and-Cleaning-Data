# Getting-and-Cleaning-Data

## step 1

test.tags <- read.table("test/y_test.txt", col.names="tag")
test.topics <- read.table("test/subject_test.txt", col.names="topic")
test.data <- read.table("test/X_test.txt")
train.tags <- read.table("train/y_train.txt", col.names="tag")
train.topics <- read.table("train/subject_train.txt", col.names="topic")
train.data <- read.table("train/X_train.txt")


dataset <- rbind(cbind(test.topics, test.tags, test.data),
              cbind(train.topics, train.tags, train.data))

## step 2

structures <- read.table("features.txt", strip.white=TRUE, stringsAsFactors=FALSE)

structures.mean.standard <- structures[grep("mean\\(\\)|std\\(\\)", structures$V2), ]


data.mean.standard <- dataset[, c(1, 2, structures.mean.standard$V1+2)]

## step 3

tags <- read.table("activity_labels.txt", stringsAsFactors=FALSE)

data.mean.standard$tag <- tags[data.mean.standard$tag, 2]

## step 4

columnames <- c("topic", "tag", structures.mean.standard$V2)

columnames <- tolower(gsub("[^[:alpha:]]", "", columnames))

colnames(data.mean.standard) <- columnames

## step 5

total.data <- aggregate(data.mean.standard[, 3:ncol(data.mean.standard)],
                       by=list(topic = data.mean.standard$topic, 
                               tag = data.mean.standard$tag),
                       mean)
write.table(format(total.data, scientific=T), "tidyfinal.txt",row.names=FALSE)
