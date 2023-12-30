
![Screenshot 2023-12-29 at 5 27 05 PM](https://github.com/soumilshah1995/Get-Started-with-Hudi-CLI-Locally-Using-Docker-in-Minutes-and-Connect-to-Your-S3-Data-/assets/39345855/7150ffe5-508f-455e-a198-5f128783b5d5)

# Steps


###  Step 1: Configure AWS Profile 

```
aws configure --profile glue4

```

### Step 2 : Clone the project and Spin up AWS GLue Container locally 
```
git clone https://github.com/soumilshah1995/docker_compose_glue4.0-hudi
Follow Steps 
```

### Step 3: Spin up container and Exec into it Through Docker Desktop or terminal 

![Screenshot 2023-12-29 at 7 08 52 PM](https://github.com/soumilshah1995/Get-Started-with-Hudi-CLI-Locally-Using-Docker-in-Minutes-and-Connect-to-Your-S3-Data-/assets/39345855/819ae31a-2ab1-4f36-adb1-7b4c1c252080)

![Screenshot 2023-12-29 at 7 09 09 PM](https://github.com/soumilshah1995/Get-Started-with-Hudi-CLI-Locally-Using-Docker-in-Minutes-and-Connect-to-Your-S3-Data-/assets/39345855/70af748d-425d-4584-aaea-cc4421810907)

### Step 4: Create a file called setup_hudi.sh
```
nano setup_hudi.sh

```

Paste this code 
```

#!/bin/bash

# Define versions
HUDI_VERSION="0.13.0"
SPARK_VERSION="3.3"
HADOOP_AWS_VERSION="3.2.0"
AWS_SDK_VERSION="1.12.626"

# Step 1: Create Directory and install JAR files
mkdir -p cli-staging/
cd cli-staging/

# Download HUDI JAR files
HUDI_CLI_JAR="hudi-cli-bundle_2.12-$HUDI_VERSION.jar"
SPARK_BUNDLE_JAR="hudi-spark$SPARK_VERSION-bundle_2.12-$HUDI_VERSION.jar"
HUDI_CLI_WITH_BUNDLE_SCRIPT="hudi-cli-with-bundle.sh"
HADOOP_AWS_JAR="hadoop-aws-$HADOOP_AWS_VERSION.jar"
AWS_SDK_JAR="aws-java-sdk-bundle-$AWS_SDK_VERSION.jar"

download_jar() {
  local url="$1"
  local filename="$2"
  curl "$url" -o "$filename"
  echo "Downloaded $filename"
}

download_jar "https://repo1.maven.org/maven2/org/apache/hudi/hudi-cli-bundle_2.12/$HUDI_VERSION/$HUDI_CLI_JAR" "$HUDI_CLI_JAR"
download_jar "https://repo1.maven.org/maven2/org/apache/hudi/hudi-spark$SPARK_VERSION-bundle_2.12/$HUDI_VERSION/$SPARK_BUNDLE_JAR" "$SPARK_BUNDLE_JAR"
download_jar "https://raw.githubusercontent.com/apache/hudi/release-$HUDI_VERSION/packaging/hudi-cli-bundle/hudi-cli-with-bundle.sh" "$HUDI_CLI_WITH_BUNDLE_SCRIPT"
download_jar "https://repo1.maven.org/maven2/org/apache/hadoop/hadoop-aws/$HADOOP_AWS_VERSION/$HADOOP_AWS_JAR" "$HADOOP_AWS_JAR"
download_jar "https://repo1.maven.org/maven2/com/amazonaws/aws-java-sdk-bundle/$AWS_SDK_VERSION/$AWS_SDK_JAR" "$AWS_SDK_JAR"

# Make hudi-cli-with-bundle.sh executable
chmod +x hudi-cli-with-bundle.sh

# Step 3: Create CONF Directory
mkdir -p conf/
cd conf/
download_jar "https://raw.githubusercontent.com/apache/hudi/release-$HUDI_VERSION/packaging/hudi-cli-bundle/conf/hudi-env.sh" "hudi-env.sh"
download_jar "https://raw.githubusercontent.com/apache/hudi/release-$HUDI_VERSION/packaging/hudi-cli-bundle/conf/log4j2.properties" "log4j2.properties"

# Set ENV VAR
cd ../
export CLI_BUNDLE_JAR="/home/glue_user/cli-staging/$HUDI_CLI_JAR"
echo "CLI_BUNDLE_JAR is set to: $CLI_BUNDLE_JAR"

export SPARK_BUNDLE_JAR="/home/glue_user/cli-staging/$SPARK_BUNDLE_JAR"
echo "SPARK_BUNDLE_JAR is set to: $SPARK_BUNDLE_JAR"

export CLIENT_JAR="/home/glue_user/cli-staging/$AWS_SDK_JAR:/home/glue_user/cli-staging/$HADOOP_AWS_JAR"
echo "CLIENT_JAR is set to: $CLIENT_JAR"

export SPARK_HOME=/home/glue_user/spark
echo "SPARK_HOME is set to: $SPARK_HOME"

```
### Step 5: Run Shell File and set Enviroment Variables 
```
chmod +x setup_hudi.sh && ./setup_hudi.sh
export AWS_REGION=us-east-1
export AWS_ACCESS_KEY_ID="XXX"
export AWS_SECRET_ACCESS_KEY="XXX"

```

### Step 6: cd into Directory and run Hudi CLI 
```
chmod +x hudi-cli-with-bundle.sh && ./hudi-cli-with-bundle.sh
connect -–path s3://XXX/bronze/table_name=customers/
commits show
```


