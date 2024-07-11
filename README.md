# Install SonarQube Scanner

![GitHub](https://img.shields.io/github/license/nosinovacao/sonarscanner-action)

## Description

This GitHub Action installs the SonarQube scanner along with Java in a platform-agnostic way.
It's designed to help seamlessly integrate SonarQube's static code analysis tools into your CI/CD pipeline.

Use this scanner if you are **not** using one of the following techs:

- Gradle (with Java, C++ or JavaScript): use the [SonarQube Scanner for Gradle](https://docs.sonarqube.org/latest/analysis/scan/sonarscanner-for-gradle/).
- Maven (with Java, C# or others): use the [SonarQube Scanner for Maven](https://docs.sonarqube.org/latest/analysis/scan/sonarscanner-for-maven/).
- Ant (for Java): use the [SonarQube Scanner for Ant](https://docs.sonarqube.org/latest/analysis/scan/sonarscanner-for-ant/).
- Python: use the [SonarQube Scanner for Python](https://docs.sonarsource.com/sonarqube/9.9/analyzing-source-code/scanners/sonarscanner-for-python/).
- .NET: use the [SonarQube Scanner for MSBuild](https://docs.sonarqube.org/latest/analysis/scan/sonarscanner-for-msbuild/).

This should cover all other cases. Check the specifics of your language/tech in the [Languages section](https://docs.sonarsource.com/sonarqube/9.9/analyzing-source-code/languages/overview/) of the SonarQube documentation.

## Why?

Well, the default sonarqube action ([sonarsource/sonarqube-scan-action](https://github.com/SonarSource/sonarqube-scan-action))
uses [a Dockerfile image](https://github.com/SonarSource/sonarqube-scan-action/blob/master/action.yml#L8-L9). That is not bad
in itself, but:

- It's not completely platform agnostic, as it requires a container runtime in the github runner. We are avoiding that in our windows runners.
- It builds every time you run the action, which we consider particularly wasteful. Also, you'll more often than not will hit the [Docker Hub rate limit](https://docs.docker.com/docker-hub/download-rate-limit/) and your builds will fail.

Is it perfect? Not really. Now you will have to issue the sonar-scanner command yourself, which is a bit of a hassle.

## Author

- NOS Inovação

## Inputs

| Name              | Description                                          | Required | Default          |
| ----------------- | ---------------------------------------------------- | -------- | ---------------- |
| `version`         | The version of the SonarQube scanner to be used      | No       | `6.1.0.4477`     |
| `javaDistribution`| The distribution of Java to be used                  | No       | `temurin`        |
| `javaVersion`     | The version of Java to be used                       | No       | `21`             |

You can check if there's a new version of the scanner [here](https://docs.sonarsource.com/sonarqube/9.9/analyzing-source-code/scanners/sonarscanner/).

## Usage

To use this action in your workflow, add the following step to your GitHub Actions configuration file (.yml):

```yaml
name: CI
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - name: Install SonarQube scanner
      uses: nosinovacao/sonarscanner-action@v1
      with:
        version: '6.1.0.4477' # Specify the version if needed
        javaDistribution: 'temurin' # Specify the Java distribution
        javaVersion: '21'    # Specify the Java version
```

Then you can use the SonarQube scanner in your workflow by running `sonar-scanner` in your project's root directory.

The following example is for a Golang project, you must adapt it to your project and language.

```yaml
    - name: Create sonar-project.properties file
      run: |
        touch sonar-project.properties
        echo "sonar.projectKey=your-project-key" >> sonar-project.properties
        echo "sonar.host.url=${{ secrets.SONARQUBE_URL }}" >> sonar-project.properties
        echo "sonar.login=${{ secrets.SONARQUBE_TOKEN }}" >> sonar-project.properties
        # point this to your sources folder
        # this is a Golang example
        echo "sonar.sources=." >> sonar-project.properties  
        echo "sonar.exclusions=**/*_test.go" >> sonar-project.properties
        echo "sonar.tests=." >> sonar-project.properties
        echo "sonar.test.inclusions=**/*_test.go" >> sonar-project.properties

    - name: Run SonarQube Scanner 
      run: sonar-scanner 
```

For test coverage, please refer to the [SonarQube documentation on test coverage](https://docs.sonarsource.com/sonarqube/9.9/analyzing-source-code/test-coverage/overview/).

## Steps

- Check if Java is installed: This step checks if Java is installed on the runner.

- Set up JDK: If Java is not installed, this step will install the specified Java distribution and version using the actions/setup-java action.

- Set up SonarQube scanner: This step downloads and unzips the SonarQube scanner to /tmp and adds its path to the system by appending it to $GITHUB_PATH.

## License

This project is licensed under the MIT License - see the LICENSE file for details.
