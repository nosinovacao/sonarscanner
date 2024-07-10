# Install SonarQube Scanner

![GitHub](https://img.shields.io/github/license/nosinovacao/sonarscanner-action)

## Description

This GitHub Action installs the SonarQube scanner along with Java in a platform-agnostic way.
It's designed to help seamlessly integrate SonarQube's static code analysis tools into your CI/CD pipeline.

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
| `installJava`     | Whether to install Java                              | No       | `true`           |
| `javaDistribution`| The distribution of Java to be used                  | No       | `temurin`         |
| `javaVersion`     | The version of Java to be used                       | No       | `21`             |

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
      uses: nosinovacao/sonarscanner-action@main
      with:
        version: '6.1.0.4477' # Specify the version if needed
        installJava: 'true'  # Whether to install Java
        javaDistribution: 'oracle' # Specify the Java distribution
        javaVersion: '22'    # Specify the Java version
```

Then you can use the SonarQube scanner in your workflow by running `sonar-scanner` in your project's root directory.

```yaml
    - name: Run SonarQube Scanner begin
      run: sonar-scanner begin -Dsonar.host.url=${{ secrets.SONARQUBE_URL }} -Dsonar.token=${{ secrets.SONARQUBE_TOKEN }} # etc…
    - name: build
      run: #run your build and tests and whatnot
    - name: Run SonarQube Scanner end and upload results
      run: sonar-scanner end
```

## Steps

- Set up JDK: If the installJava input is set to true, this step will install the specified Java distribution and version using the actions/setup-java action.

- Set up SonarQube scanner: This step downloads and unzips the SonarQube scanner to /tmp and adds its path to the system by appending it to $GITHUB_PATH.

## License

This project is licensed under the MIT License - see the LICENSE file for details.