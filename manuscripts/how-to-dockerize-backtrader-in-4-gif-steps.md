- How to Dockerize Backtrader in 4 GIF Steps
  
  [Backtrader](https://www.backtrader.com/) is "a feature-rich Python framework for backtesting and trading."
  
  Backtrader isn't just for backtesting strategies. It's also has live trading and is integrated with InteractiveBrokers ["IB"], Oanda, VisualChart, Alpaca, ccxt, etc. Using your own dockerized Backtrader platform, you don't have to be tied down to any operation system or third-party platforms that provide backtesting functions. And if you're a serious trader, you can create your own stock, forex, crypto or even a deep machine learning trading robot to trade automatically.
- Introduction
  
  **TL;DR**
  
  In this article, you will dockerize Backtrader on your Windows machine. In the Docker container, you will execute the example [Hello Algotrading](https://www.backtrader.com/home/helloalgotrading/) python script, which outputs a visual result of the backtesting strategy. You will have access to thousands of Data Science libraries using the Anaconda package manager.
- Prerequisites
  
  * [Docker Desktop for Windows](https://hub.docker.com/editions/community/docker-ce-desktop-windows). Requires Microsoft Windows 10 Professional or Enterprise 64-bit.
  
  * [Windows Powershell.](https://github.com/powershell/powershell) Requires Administrative privilege.
  
  * [VcXsrv Windows X Server.](https://sourceforge.net/projects/vcxsrv/) The tutorial [Run GUI App in Linux Docker Container on Windows Host](https://dev.to/darksmile92/run-gui-app-in-linux-docker-container-on-windows-host-4kde) walks you through installing and configuring your XLaunch program.
- Step 1 - Pull a Pre-Defined Docker Image 
  
  There are many pre-defined Docker images on the cloud, from both official and unofficial partners.
  
  The are many reasons for choosing an image, such as reliablity, speed, size, backward compatibility, etc.
  
  Why did I choose the Docker Anaconda3 image?
  
  The benefits of choosing the above pre-defined image are as follows:
  
  * Anaconda is a package manager for Python Data Science libraries
  
  * Python (with matplotlib) comes prepackaged
  
  * It runs in root administrator mode.
  
  The only downside that I can see is that the image takes a whopping 2.6 Gigabyte of hard disk space!
  
  Type the following command in a Powershell or Command Prompt ["CMD"] to pull the Docker Anaconda image:
  
     PS> docker pull continuumio/anaconda3
  
  Note: The Docker Anaconda3 image has Python 3.5 installed by default. If you want to use Python 2.7 instead, then you should pull the *continuumio/anaconda* image.
  
  
  
  ![][1]
  
  [1]: images/how-to-dockerize-backtrader-in-4-gif-steps/step-1---pull-a-pre-defined-docker-image-.gif
- Step 2 - Configure Docker to Enable XLaunch as Display
  
  In this article, we will show you how to enable the Graphical User Interface ["GUI"] of a Docker Linux container from a Windows 10 host system.
  
  Why would we want to do that?
  
  Although Backtrader can be executed solely from the terminal, however, some of us may prefer to have a visual output for our backtesting strategies.
  
  First, to find your Docker's IP address, type the following command in Powershell:
  
     PS> ipconfig
  
  Search for the Ethernet adapter called *DockerNAT*, and note down the IPv4 Address. For example:
  
     IPv4 Address. . . . . . . . : 10.0.75.1
  
  In the Powershell terminal, type the following command:
  
     PS> set-variable -name DISPLAY -value <ip-address>:0.0
  
  where <ip-address> is your Docker's IP address. 
  
  Note: Remember to append *:0.0* to the end of your IP address.
  
  ![][2]
  
  [2]: images/how-to-dockerize-backtrader-in-4-gif-steps/step-2---configure-docker-to-enable-xlaunch-as-display.gif
- Step 3 - Create a Docker Container from Our Image
  
  Type the following command in PowerShell to create a folder, e.g. *anaconda3*, which will be mounted as a shared folder in the new Docker container:
  
     PS> mkdir anaconda3
  
  Then, type the following command to create a Docker container:
  
     PS> docker run -v d:\docker\anaconda3\:/home --name objAnaconda3 -e DISPLAY=$DISPLAY -it continuumio/anaconda3
  
  These inputs require some explanation.
  
  **Bind Mount a Volume**
  
  By design, Docker containers do not store persistent data. Once the container stops, any data written to it will no longer be available. To solve this issue of persisting data from a container, Docker has two options: (a) bind mounts; (b) volumes.
  
  In the above example, we use the first option, where a Windows folder (host) is bound to a Docker container folder using the -v option as follows:
  
     -v <host-folder>:<container-folder> 
  
  **Container Name**
  
  While the input --name is optional, we use it to give the container a reference name, which we can use later to restart the container as follows:
  
     --name <reference-name>
  
  **Set Environment Variable**
  
  We can set the environment variables for the Docker container as follows:
  
     -e <variable-name>=<variable-value>
  
  **Interactive Terminal**
  
  We can tell Docker to run the container in an interactive mode by using the input -it as follows:
  
     PS> docker run -it <image-name>
  
  Note that all the other inputs are optional, and the only input required for the **Docker run** command is the *image-name*.
  
  Next, type the following command in the Docker bash terminal:
- pip install backtrader[plotting]
  
  Stop the Docker container and return command back to Powershell:
- exit
  
  ![][3]
  
  [3]: images/how-to-dockerize-backtrader-in-4-gif-steps/step-3---create-a-docker-container-from-our-image.gif
- Step 4 - Execute Hello Algotrading Python Script
  
  The final step is to restart our previous container, and run our Python scripts.
  
  Ensure that you're in the mounted folder of the container.
  
  Create a file called *basic.py* and insert the following code:
  
     from __future__ import (absolute_import, division, print_function,
                             unicode_literals)
     import backtrader as bt
     if __name__ == '__main__':
         cerebro = bt.Cerebro()
         print('Starting Portfolio Value: %.2f' % cerebro.broker.getvalue())
         cerebro.run()
         print('Final Portfolio Value: %.2f' % cerebro.broker.getvalue())
  
  Next, create another file called *sma.py* and insert the following code:
  
     from datetime import datetime
     import backtrader as bt
- Create a subclass of Strategy to define the indicators and logic
     class SmaCross(bt.Strategy):
- list of parameters which are configurable for the strategy
         params = dict(
             pfast=10,  # period for the fast moving average
             pslow=30   # period for the slow moving average
         )
         def __init__(self):
             sma1 = bt.ind.SMA(period=self.p.pfast)  # fast moving average
             sma2 = bt.ind.SMA(period=self.p.pslow)  # slow moving average
             self.crossover = bt.ind.CrossOver(sma1, sma2)  # crossover signal
         def next(self):
             if not self.position:  # not in the market
                 if self.crossover > 0:  # if fast crosses slow to the upside
                     self.buy()  # enter long
             elif self.crossover < 0:  # in the market & cross to the downside
                 self.close()  # close long position
     cerebro = bt.Cerebro()  # create a "Cerebro" engine instance
- Create a data feed
     data = bt.feeds.YahooFinanceData(dataname='MSFT',
                                      fromdate=datetime(2011, 1, 1),
                                      todate=datetime(2012, 12, 31))
     cerebro.adddata(data)  # Add the data feed
     cerebro.addstrategy(SmaCross)  # Add the trading strategy
     cerebro.run()  # run it all
     cerebro.plot()  # and plot it with a single command
  
  Type the following command in PowerShell to restart our previous container:
  
     PS> docker start -ia objAnaconda3
  
  Then, in the Docker interactive terminal, type the following commands to run both scripts:
- cd /home
- python basic.py
- python sma.py
  
  Your project folder should look like this:
  
     docker/
       +- anaconda3/
           |- basic.py
           |- sma.py
  
  ![][4]
  
  [4]: images/how-to-dockerize-backtrader-in-4-gif-steps/step-4---execute-hello-algotrading-python-script.gif
- Conclusion
  
  In this article, you used Docker pull command to download an image of Anaconda package manager from DockerHub.com. Then, you created an instance of this image using the Docker run command, which mounted a local folder and started an interactive terminal within this container. In this terminal, you installed Backtrader with plotting feature and you executed the [Hello Algotrading](https://www.backtrader.com/home/helloalgotrading/) python script.
- What To Do Next
  
  You can further explore Backtrader in several meaningful ways:
  
  * Install [Technical Analysis Library](http://ta-lib.org/) - This allows you to perform technical analysis of financial market data. There is a free [Python wrapper for TA-Lib](https://mrjbq7.github.io/ta-lib/).
  
  * Read [Backtrader Documentation](https://www.backtrader.com/docu/) - This helps you to have a deeper knowledge of the Backtrader engine.
  
  * Read [Backtrader Blog](https://www.backtrader.com/blog/) - This helps you to find relevant information about Backtrader strategies.
  
  * Participate in [Backtrader Community](https://community.backtrader.com/) - This helps you to have meaningful discussion online with other users of Backtrader.
  
  * Participate in [Python for Telegram](https://t.me/joinchat/DmGkrhIE_g6Mk-zJS6sWgA) - This helps you to chat online with other users of Backtrader in Telegram.
  
  * Install [Python Data Science Packages](https://www.anaconda.com/distribution/) - This allows you to manage Python Data Science libraries with Anaconda.