# Environment

## Prerequisites

* Maven CLI;
* Git CLI;
* Java 11;
* An IDE of your preference;
* If using Windows, PowerShell is recommended;
* Helidon CLI _(optional)_;
* REST client tools like cURL or postman _(Optional)_;

!!! INFO

    The helidon CLI is needed for only on optional labs where you can experience some features that are only available through this tool. 

To check if you have the above tools installed, open your terminal and running the following the commands. You should be able to see the version of each of the tools:

```shell
java -version
mvn -version
git --version
```

### Installing helidon CLI _(optional)_

To install Helidon, you can run the commands below according to your O.S:

MacOS:
```bash
curl -O https://helidon.io/cli/2.3.3/darwin/helidon
chmod +x ./helidon
sudo mv ./helidon /usr/local/bin/
```

Linux:
```bash
curl -O https://helidon.io/cli/2.3.3/linux/helidon
chmod +x ./helidon
sudo mv ./helidon /usr/local/bin/
```

Windows:
```bat
PowerShell -Command Invoke-WebRequest -Uri "https://helidon.io/cli/2.3.3/windows/helidon.exe" -OutFile "C:\Windows\system32\helidon.exe"
```

You can verify the installation by running in the terminal:

```shell
helidon version
```

Helidon CLI is available for Helidon 2+.

## Prepare your working directory

Some exercises will start with preconfigured projects that you should have available in your development environment. To get the projects, please clone the following git repository: 

```shell
git clone https://github.com/architects4j/helidon-microstream-training-labs-foundation
```

