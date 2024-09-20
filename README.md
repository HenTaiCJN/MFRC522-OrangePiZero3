MFRC522-OrangePiZero3
==============

## 前言
为验证OrangePiZero3的spi通道能否正常使用, 尝试连接手头上有的spi rc522 rfid读卡器.  

故而发现现有的资料都是至少七年前的, 原MFRC522-python存储库也已经停止更新.   

原MFRC522-python存储库需要使用SPI-Py, 而SPI-Py更新后MFRC522-python存储库未更新,导致无法正常使用.  

MFRC522-python基于python2编写. 基于能跑就别动的原则, 本项目将运行于python2.  

如果有强python3需求, 其实也
只用将print语法改为python3的print语法即可. (但本人无法保证100%正常运行)

## 通用性  
MFRC522.py这个驱动库本质上是直接对挂载的spi通道和RST引脚进行操作.  

所以只要当前设备存在/dev/spidev*,并且能通过python操作RST引脚,理论上都能使用.  


## 引脚参考


| Name | GPIO # |  Pin name  |
|:------:|:------:|:----------:|
| SDA  |  229   |    PH5     |
| SCK  |  230   |    PH6     |
| MOSI |  231   |    PH7     |
| MISO |  232   |    PH8     |
| IRQ  |  None  |    None    |
| GND  |  Any   | Any Ground |
| RST  |   74   |    PC10    |
| 3.3V |  Any   |  Any 3V3   |

tip1:   

SDA对应是spi的CS引脚, SCK对应的是spi的CLK引脚, IRQ悬空不接, GND和3.3V对应接上就行, RST引脚接在未被占用的引脚即可.  

tip2: 

SDA SCK MOSI MISO这四个引脚只要插上自己挂载的spi通道对应引脚即可, 我开启的是spi1-cs0-spidev;  
RST引脚,例如我接的是PC10,则需要在MFRC522.py中将常量`NRSTPD`修改为对应的Pin Name即可;  
因为代码中我使用的是GPIO.setmode(GPIO.SUNXI),所以可以直接识别字符串的引脚名称.  

tip3:  
orangepizero3官方推荐的是通过orangepi-config开启spi1-cs1-spidev, 因为cs0对应的PH5被I2C3占用.  
如果开启的为cs1即挂载的spi为`/dev/spi1.1`则将PH5改为PH9.  
详情可以查看orangepi官方wiki,这里不再赘述.  

## 依赖安装
1.安装python2  

```shell
sudo apt install python2 python2-dev
```
2.安装python2的pip  

```shell
curl https://bootstrap.pypa.io/pip/2.7/get-pip.py -o get-pip.py
python2 get-pip.py
```
3.安装SPI-Py
```shell
git clone https://github.com/lthiery/SPI-Py.git
cd SPI-Py
python2 -m pip install .
```
4.安装OPi.GPIO
```shell
git clone https://github.com/rm-hull/OPi.GPIO.git
cd OPi.GPIO
python2 -m pip install .
```
## 运行
运行前需要将MFRC522.py中初始化时的`dev`路径修改为自己设备上挂载的spi路径. 也可在Read.py中的实例化代码中显式指定.  
```shell
python2 Read.py
```
最后,运行Read.py,将卡片放置于读卡器,即可查看UID.

## License
This code and examples are licensed under the GNU Lesser General Public License 3.0.
