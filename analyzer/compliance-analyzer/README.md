<p align="center">
    <img src="https://user-images.githubusercontent.com/45048351/91091340-1c578200-e65f-11ea-9c5d-597fbbe4ba41.jpg">
</p>
<br/>
<p align="center">
    <a href="https://github.com/fasten-project/fasten/actions" alt="GitHub Workflow Status">
        <img src="https://img.shields.io/github/workflow/status/fasten-project/fasten/Java%20CI?logo=GitHub%20Actions&logoColor=white&style=for-the-badge" /></a>
    <!-- Here should be a link to Maven repo and version should be pulled from there. -->
    <a href="https://github.com/fasten-project/fasten/" alt="GitHub Workflow Status">
                <img src="https://img.shields.io/maven-central/v/fasten/graph?label=version&logo=Apache%20Maven&style=for-the-badge" /></a>
</p>
<br/>

The FASTEN QMSTR plugin is a tool to integrate the [QMSTR](https://github.com/qmstr/qmstr) license and compliance analysis process into FASTEN.\
This integration is part of the WP4 and it's being developed by [Endocode AG](https://endocode.com).

## Arguments
- `-r` `--repo` `--repository`                Path to JSON file containing repository information

## Usage 

<p align="center">
    <img src="https://raw.githubusercontent.com/fullsushidev/qmstr/master/doc/static/img/qmstr-plugin.gif">
</p>

#### <!-- TODO ultimate goal -->
1. Start the plugin specifying the path to the cluster credentials file as an environment variable:
    ```bash
    # Example: from the FASTEN root folder
    mvn \
      -DclusterCredentials=path/to/cluster/credentials.json \
      clean install exec:java \
      -f analyzer/compliance-analyzer/pom.xml \
      -Dexec.args="--repository analyzer/compliance-analyzer/dummyKafkaTopic.json"
    ```
   This demo simulates a Kafka topic consumption by reading the [`dummyKafkaTopic.json` file](dummyKafkaTopic.json).\
   Upon topic consumption, the `compliance-analyzer` launches Quartermaster that will build the specified repository. 

1. Wait for the build and analysis phases to be over:
    ```bash
    kubectl logs --follow $(kubectl get pods --selector job-name=qmstr -o=name) qmstr-client
    ```

1. Forward two local ports to the following two ports on the DGraph Pod:
    ```bash
    kubectl port-forward $(kubectl get pods --selector job-name=qmstr -o=name) 8000:8000
    ```
    ```bash
    kubectl port-forward $(kubectl get pods --selector job-name=qmstr -o=name) 8080:8080
    ```

1. Open [localhost:8000/?latest](http://localhost:8000/?latest) in your browser.

1. Click on "Continue":
    <p align="center">
        <img src="https://user-images.githubusercontent.com/45048351/92643192-0649f280-f2ea-11ea-842d-ee9612f54f7c.png" alt="DGraph login page" width="75%"/>
    </p>

1. Navigate to the "Console" page.

1. You should now be able to query the database:  
    ```graphql
    {
        PackageNodes(func: has(packageNodeType)) @recurse(loop: true, depth: 3) {
            uid
            name
            version
            packageNodeType
            targets
            additionalInfo
            buildConfig
            diagnosticInfo
            timestamp
        }
    
        FileNodes(func: has(fileNodeType)) @recurse(loop: true, depth: 3) {
            uid
            fileNodeType
            path
            name
            fileData
            timestamp
            derivedFrom
            dependencies
        }
    
        FileDataNodes(func: has(fileDataNodeType)) @recurse(loop: true, depth: 3) {
            uid
            fileDataNodeType
            hash
            additionalInfo
            diagnosticInfo
        }
    
        InfoNodes(func: has(infoNodeType)) @recurse(loop: true, depth: 3) {
            uid
            infoNodeType
            type
            confidenceScore
            analyzer
            dataNodes
            timestamp
        }
    
        Analyzers(func: has(analyzerNodeType)) @recurse(loop: true, depth: 3) {
            uid
            name
            analyzerNodeType
            trustLevel
            pathSub
            old
            new
        }
    
        DataNodes(func: has(dataNodeType)) @recurse(loop: true, depth: 3) {
            uid
            dataNodeType
            type
            data
            timestamp
        }
    }
    ```

1. The generated graph should look something like this:
    <p align="center">
        <img src="https://github.com/endocode/qmstr/blob/feature/self-contained-modules/deploy/img/graph.png?raw=true" alt="Generated Build Graph example"/>
    </p>
    The left part of the graph consists in the usual build graph, having in this case a single (Java) package node in green as the central node.
    License and compliance information is on the right, having the analyzer node in pink right in the middle.


## Join the community

The FASTEN software package management efficiency relies on an open community contributing to open technologies. Related research projects, R&D engineers, early users and open source contributors are welcome to join the [FASTEN community](https://www.fasten-project.eu/view/Main/Community), to try the tools, to participate in physical and remote worshops and to share our efforts using the project [community page](https://www.fasten-project.eu/view/Main/Community) and the social media buttons below.  
<p>
    <a href="http://www.twitter.com/FastenProject" alt="Fasten Twitter">
        <img src="https://img.shields.io/badge/%20-Twitter-%231DA1F2?logo=Twitter&style=for-the-badge&logoColor=white" /></a>
    <a href="http://www.slideshare.net/FastenProject" alt="GitHub Workflow Status">
                <img src="https://img.shields.io/badge/%20-SlideShare-%230077B5?logo=slideshare&style=for-the-badge&logoColor=white" /></a>
    <a href="http://www.linkedin.com/groups?gid=12172959" alt="Gitter">
            <img src="https://img.shields.io/badge/%20-LinkedIn-%232867B2?logo=linkedin&style=for-the-badge&logoColor=white" /></a>
</p>