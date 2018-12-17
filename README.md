### **** Confluent released experimental binary wheel distributions for Windows  in version 0.11.6 ***
I upgraded successfully by runnig follow command:
python -m pip install confluent-kafka --upgrade



# confluent-kafka-python
### **** keep your Confluent Kafka Python development on Windows ****

As of June 11, 2018, the day I create this repository, Confluent was not offically support Python package on Windows yet. I spent some time and managed to make the Python package working on Windows platform. In this case I could keep my development environment on Windows computer, which makes my life much easier.

If you fought the same problem like me, you probably will be able to find something helpful. I'll try explain the details so you could actullay do it by yourself, if you don't feel confortable to install the pre-compiled dll / bin file on your system.

## Compatibility

I only work on Python 3.6 X64 version, so you might need do your compiling if you want use 32 bit version Python, or 2.7. I tested on Windows 10 as my development environment and deployed to Windows server 2012R2, the package works well on both.

## Where these files come from

#### confluent-kafka-python-0.11.4
I cloned from https://github.com/confluentinc/confluent-kafka-python, as of May 31, 2018, v 0.11.4 is the latest release
#### librdkafka-0.11.4
I cloned from https://github.com/edenhill/librdkafka, I pick the same verion as Confluent's package, v 0.11.4. PS: The librdkafka repo on Confluent is not up-to-date, some functions are missing in the repo.
#### librdkafka-reference
A simply re-org folder include necessary header/c files for compiling, and a release folder with pre-compiled dll/lib files. You will alwasy need this for runtime anyway. 

#### what I changed
1, remove item from ignore file so I could upload the build folder under C:\confluent-kafka-python\confluent-kafka-python-0.11.4<br>
2, changed setup.py file to make sure it can find the correct dll file, from "libraries=['rdkafka']" to "libraries=['librdkafka']". Not sure it's a bug/typo from Confluent, or the difference between Linux and Windows.<br>
3, added the following  definition in kafka.h file to address the compatible issue on Windows C++ library.<br>

#ifdef _MSC_VER <br>
//not #if defined(_WIN32) || defined(_WIN64) because we have strncasecmp in mingw<br>
#define strncasecmp _strnicmp<br>
#define strcasecmp _stricmp<br>
#endif<br>


## Prepare for installation

Clone this repo to your C driver. It's highly recommended to cloen to the folder c:\confluent-kafka-python, it will make the rest of this document much easier.

Download & install VC++ 2013 redistribution package, you can download from Microsoft @ https://www.microsoft.com/en-us/download/details.aspx?id=40784

Optional, if you want to build by youself, plase download and install OpenSSL package from https://slproweb.com/products/Win32OpenSSL.html. Again, it's a pre-compiled package, and I won't recommend you to compile OpenSSL by yourself, it will require more source codes to be downloaded to your local disk.... You can download both 32 & 64-bit but not the latest version. Version v 1.0.2 works, not the latest one.

## Set up system variables

Please set up the following 3 system variables before installing/compiling. If you clone this repo to a different location on your local driver, please change the value of variable accordingly.

#### INCLUDE=C:\confluent-kafka-python\librdkafka-reference
#### LIB=C:\confluent-kafka-python\librdkafka-reference\release
#### PATH=C:\confluent-kafka-python\librdkafka-reference\release

* if you won't build by yourself, probably you don't need INCLUDE & LIB been set. Add to the current PATH varible is required for runtime.

## Install Python pacakge

#### start a new command windows after setting up the system variables above
#### cd C:\confluent-kafka-python\confluent-kafka-python-0.11.4
#### python setup.py install

It'll be very quick, since it simply install the pre-compiled pyd file.

## Test package

#### cd c:\ ( or any other folder which is NOT the Kafka installation folder )
#### python
#### => import confluent_kafka
#### => (should get nothing here, enjoy!!! )

If you get "missing dll" error when importing the package, the possible reason might be:<br>
1, VC++ 2013 distribution package was not installed properly.<br>
2, the release folder is not added to PATH variable<br>

## DIY the build

If you want to build the pacakge by yourself, please following the steps below.<br>
1, delete the build folder from C:\confluent-kafka-python\confluent-kafka-python-0.11.4<br>
2, run "python setup.py install" will build the Python pacakge first<br>

if you want to build librdkafka dll, following the steps below.<br>
1, install VS2013<br>
2, install OpenSSL @ C:\OpenSSL-Win64<br>
3, open the solution under C:\confluent-kafka-python\librdkafka-0.11.4\win32<br>
4, change the build to X64 platform and release version<br>
5, build the project<br>
6, copy the dll from C:\confluent-kafka-python\librdkafka-0.11.4\win32\outdir\v120\x64\Release to C:\confluent-kafka-python\librdkafka-reference\release<br>
7, repeat the previous step to install Python pacakge<br>

Let me know if you have any difficulty from building librdkafka project, I'm glad to help. 

:p


