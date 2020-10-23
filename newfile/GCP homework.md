



# GCP homework

### GCP account (25%): 

![Screen Shot 2020-10-22 at 5.16.41 PM](/Users/sue/Library/Application Support/typora-user-images/Screen Shot 2020-10-22 at 5.16.41 PM.png)



### Source code (25%):

```java
import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class WordCount {

    public static class TokenizerMapper
            extends Mapper<Object, Text, Text, IntWritable>{

        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context
        ) throws IOException, InterruptedException {
            StringTokenizer itr = new StringTokenizer(value.toString());
            while (itr.hasMoreTokens()) {
                word.set(itr.nextToken());
                context.write(word, one);
            }
        }
    }

    public static class IntSumReducer
            extends Reducer<Text,IntWritable,Text,IntWritable> {
        private IntWritable result = new IntWritable();

        public void reduce(Text key, Iterable<IntWritable> values,
                           Context context
        ) throws IOException, InterruptedException {
            int sum = 0;
            for (IntWritable val : values) {
                sum += val.get();
            }
            result.set(sum);
            context.write(key, result);
        }
    }

    public static void main(String[] args) throws Exception {
      	// record start time
        long startTime = System.currentTimeMillis();
        Configuration conf = new Configuration();
        Job job = Job.getInstance(conf, "word count");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
      	// record end time
        long endTime = System.currentTimeMillis();
      	// print out execution time duration
        System.out.println("Execution time: " + (endTime-startTime) + " ms");
    }
}

```



### Run WordCount.jar on GCP (50%)


```bash
yarn jar WordCount.jar WordCount /wordcountfiles/ /tmp/wordCount_result/
```

#### Execution time: 927 ms

![Screen Shot 2020-10-22 at 5.11.33 PM](/Users/sue/Library/Application Support/typora-user-images/Screen Shot 2020-10-22 at 5.11.33 PM.png)



#### The input file "input.txt" contains only "Hello World", results are as expected. ![Screen Shot 2020-10-22 at 5.14.18 PM](/Users/sue/Library/Application Support/typora-user-images/Screen Shot 2020-10-22 at 5.14.18 PM.png)

