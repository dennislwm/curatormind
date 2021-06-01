- Building Both A/B Test and Indicator-Agnostic Classes with OOP in MT4
- Introduction
  
  [Telegram](https://telegram.org/) isn't just for sending and receiving chat messages. It's also for automating your dialog flow, including work flow. Using a Telegram Bot gives you the ability to check prices, query status, manage trades, and even have a fun conversation. And if you're a serious crypto or forex trader, you can create your own Telegram Bot to manage your order flow.
  
  In this tutorial you'll use a Telegram Bot to query your orders on a Metatrader 4 account. You'll create a Telegram Bot ["bot"], build an Expert Advisor ["EA"] that can listen and process messages from a user, as well as reply to the user with orders and account data.
- Prerequisites
  
  * Metatrader 4 ["MT4"] client and demo account with any broker.
  * Telegram Bot created in your Telegram account. The tutorial [How to Create a New Telegram Bot](https://github.com/dennislwm/MT4-Telegram-Bot-Recon) walks you through creating a bot and configuring your MT4 client.
- Step 1 - Interpreting an MT4 Indicator on Charts
  
  In this section, let's take a look at how to interpret an MT4 indicatoir on charts, in particular the SRSI indicator.
  
  If SRSI crosses above the +Neutral (+0.05) level, the SRSI line changes to green to indicate a possible buy position and hold it while SRSI remains above the -Neutral (-0.05) level. Close the position when SRSI line crosses below the -Neutral level.
  
  Conversely, if SRSI crosses below the -Neutral (-0.05) level, the SRSI changes to red to indicate a possible sell position and hold it while SRSI remains below the +Neutral (+0.05) level. Close the position when SRSI line crosses above the +Neutral level.
  
  First, we create an ENUM type to define the different states that we would like to use in our strategies:
  
     enum ENUM_ZONE_LINE
     {
       ZONE_LINE_NA,                 // Not available
       ZONE_LINE_POSITIVE_MOMENTUM,  // Line is green and above +NeutralLevel
       ZONE_LINE_POSITIVE_RETREAT,   // Line is green and below +NeutralLevel
       ZONE_LINE_NEGATIVE_MOMENTUM,  // Line is red and below -NeutralLevel
       ZONE_LINE_NEGATIVE_RETREAT    // Line is red and above -NeutralLevel
     }
  
  The definition above creates different states but it does not interpret them as either bullish or bearish.
  
  We will have to assign a meaning to the above states in our A/B testing class.
  
  We refer to this as an indicator-agnostic class is because the definition does not refer to any specific indicator levels. 
  
  For example, if we have a horizontal channel that splits the indicator line into three zones, then we could reuse this class for that indicator.
  
  Second, we create another ENUM type to define the slope of the line:
  
     enum ENUM_SLOPE_LINE
     {
       SLOPE_LINE_NA,    // Not available
       SLOPE_LINE_UP,    // Line is sloping upwards
       SLOPE_LINE_FLAT,  // Line is flat
       SLOPE_LINE_DOWN   // Line is sloping downwards
     }
  
  We can create as many ENUM types for as many states as we want, but we will still need to assign meanings to them in our A/B testing class
  
  
  
  ![][1]
  
  [1]: images/building-both-a-b-test-and-indicator-agnostic-classes-with-oop-in-mt4/step-1---interpreting-an-mt4-indicator-on-charts.png
- Step 2 - Object-Oriented Programming in MT4
  
  Before diving into the Indicator class code, let's understand why we are using Object-Oriented ["OO"] Programming  in MT4.
  
  The benefits of OO programming is that we can reuse our code, and create a framework to guide our functional programming.
  
  There is a multi-to-multi relationship between a strategy and indicator, i.e. a strategy can use multiple indicators, while an indicator can be used in multiple strategies. 
  
  Throw in multiple currency pairs and a couple of different timeframes, you could get an explosive growth in code that will quickly become messy in a short period of time.
  
  There are two **ROOT** classes, namely **CStyleBuffer** and **CStyleAbtest**.
  
  First, the class **CStyleBuffer **is the **ROOT** class of all the Indicator classes.
  
  It contains all the ENUM types because the states are reusable and often exists in more than one indicator.
  
  Also, the parent class contains variables for both the symbol and timeframe of the chart and its name, which are common to all indicators. 
  
     protected:
     //--- available through methods
       int     period;
       string  symbol;
       string  name;
  
  The method Init() initializes the above variables, while intPeriod(), strPeriod(), strSymbol() and strName() methods retrieves them for use in your code.
  
       void Init(int p, string s, string n=""){period=p;symbol=s;name=n;}
  
  The Constructor CStyleBuffer(void) doesn't do anything except initializes symbol to NULL and period to zero.
  
       CStyleBuffer(void){period=0;symbol=NULL;}
  
  The method isNewBar() is included in every child class, to keep track of the new bar for every indicator. This function can be called in your code to ensure that you only refresh the indicator when there is a new bar.
  
  Finally, we declare a virtual function blnIndicatorValues() that return true if the indicator values were read correctly, or false otherwise. These virtual functions can only be implemented in the child class.
  
     //--- virtual means override possible
       virtual bool blnIndicatorValues (double& b[]) {return false;}
       virtual bool blnIndicatorValues (double& b1[], double& b2[]) {return false;}
       virtual bool blnIndicatorValues (double& b1[], double& b2[], double& b3[]) {return false;} 
  
  If your implemented function blnIndicatorValues() in the child class does not conform to any of the above virtual signatures in the parent class, then the MT4 compiler will throw an error.
- Step 3 - Creating an Indicator-Agnostic Class
  
  In this section, let's create a new class in MetaEditor, and name the class **CIndChannelLevel**.
  
  Get the source code for the above MQH file.
  
  First, we include the parent class **CStyleBuffer** and inherit from it as follows:
  
     #include <CStyleBuffer.mqh>
     class CIndChannelLevel: public CStyleBuffer {}
  
  Next, we defined some variables, in addition to symbol, period, and name, which already exists through inheritance.
  
     protected:
     //--- available through methods
       ENUM_ZONE_LINE  zone;
       ENUM_SLOPE_LINE slope;
  
  Then, we define a Constructor to initialize the above variables as follows:
  
     public:
     //--- available globally
       CIndChannelLevel(void):CStyleBuffer(){zone=ZONE_LINE_NA;slope=SLOPE_LINE_NA;}
  
  Finally, we have to override the blnIndicatorValues() function from the parent class.
  
  This function will accept indicator buffers as input parameters and assign the states to the enum types.
  
  Although the SRSI has one indicator buffer, however we override the virtual function() with three input parameters: (1) Upper NeutralLevel (2) Lower NeutralLevel and (3) SRSI indicator
  
     bool blnIndicatorValues (double& b1[], double& b2[], double& b3[])
     {
     //--- local variables
       double uppChannel, lowChannel, line;
     
     //--- Assert buffer is valid
       if( b1[0]==EMPTY_VALUE ) return false;
       if( b2[0]==EMPTY_VALUE ) return false;
       if( b3[0]==EMPTY_VALUE ) return false;
     
       uppChannel=b1[0];
       lowChannel=b2[0];
       line=b3[0];
     
     }
- Step 4 - Creating