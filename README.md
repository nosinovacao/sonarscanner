# Install SonarQube Scanner

![GitHub](https://img.shields.io/github/license/nosinovacao/sonarscanner-action)

## Description

This GitHub Action installs the SonarQube scanner along with Java in a platform-agnostic way. 
It's designed to help seamlessly integrate SonarQube's static code analysis tools into your CI/CD pipeline.

## Author

- NOS Inovação

## Inputs

| Name              | Description                                          | Required | Default          |
| ----------------- | ---------------------------------------------------- | -------- | ---------------- |
| `version`         | The version of the SonarQube scanner to be used      | No       | `6.1.0.4477`     |
| `installJava`     | Whether to install Java                              | No       | `true`           |
| `javaDistribution`| The distribution of Java to be used                  | No       | `oracle`         |
| `javaVersion`     | The version of Java to be used                       | No       | `22`             |

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

    # Add your SonarQube analysis step here
    - name: Run SonarQube Scanner
      run: sonar-scanner
```

## Steps

- Set up JDK: If the installJava input is set to true, this step will install the specified Java distribution and version using the actions/setup-java action.

- Set up SonarQube scanner: This step downloads and unzips the SonarQube scanner to /tmp and adds its path to the system by appending it to $GITHUB_PATH.

## License

This project is licensed under the MIT License - see the LICENSE file for details.