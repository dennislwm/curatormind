- Zero-Lag CurrencySlopeStrength Indicator
  
  **TL;DR**
  
  The CurrencySlopeStrength ["CSS"] indicator measures the strength of the currencies, however it does not measure the rate of change (or slope) of the strength. By integrating the GradientVolatility indicator with the CSS, i.e. GradientCSS indicator, you can have a zero-lag CSS indicator.
- Introduction
  
  I have been busy working on my own trading ideas. Below is an example of a component that I am working on, which is part of a bigger picture to develop my own set of tools and indicators, for a complete trading system.
  
  My system has six key components: (i) multi-pair; (ii) market strength; (iii) trend; (iv) momentum; (v) price action; and (vi) parameter-less. Each individual component is not new, however this system will be unique and different from others as it uses my own integration of custom indicators. I may post again to further elaborate on my system in the near future.
- GradientVolatility Integrated with CSS
  
  As mentioned above, one of my own trading idea was to integrate the GradientVolatility indicator with the CurrencySlopeStrength (CSS) indicator. See the attached picture below.
  
  The CSS measures the strength of the currencies, however it does not measure the rate of change (or slope) of the strength. For example, in the picture, the CSS showed that AUD is negative and decelerating, but it does not show the rate of deceleration. The rate of change is captured using the GradientCSS.
  
  There is a lot of potential for this indicator, as shown yesterday, when I gained a total of +73 pips from two long EurNzd trades based on their rates of change. See the attached history (ignore the other two non-EurNzd trades).
  
  ![][1]
  
  [1]: images/049-zero-lag-currencyslopestrength-indicator/gradientvolatility-integrated-with-css.png
- Trade with the GradientCSS Indicator
  
  The best part for trading on this indicator is that there won't be any optimization, as there are very few parameters. Hence, I do not have to worry about a high variance (over-fitting).
  
  Note: There is a problem when calling the GradientCSS indicator with the iCustom() function, as it does not seem to like R very much.
  
  ![][2]
  
  [2]: images/049-zero-lag-currencyslopestrength-indicator/trade-with-the-gradientcss-indicator.png
- Conclusion
  
  Just an update, I have completely rewritten the GradientCSS indicator from the ground up to use MQL code only, by replacing the lm() function from R. Then the iCustom() function should be able to work with this indicator.
  
  The GradientCSS indicator and its source code is available to my Patron subscribers.