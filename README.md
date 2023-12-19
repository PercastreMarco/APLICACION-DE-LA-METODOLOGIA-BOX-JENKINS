<picture>
 <source media="(prefers-color-scheme: dark)" srcset="https://pbs.twimg.com/media/GBNo8Una0AAFLxS?format=png&name=900x900">
 <source media="(prefers-color-scheme: light)" srcset="https://pbs.twimg.com/media/GBNo8Una0AAFLxS?format=png&name=900x900">
 <img alt="La aplicación de éste modelo es útil para la valoración de acciones y empresas, así como la predicción de la volatilidad." src="https://pbs.twimg.com/media/GBNo8Una0AAFLxS?format=png&name=900x900">
</picture>

# Application of the Box-Jenkins Methodology

The Box-Jenkins methodology has been used in a wide range of applications, including the following:

- Financial forecasting: The Box-Jenkins methodology is used to forecast stock prices, interest rates, and other financial indicators.
 
- Weather forecasting: The Box-Jenkins methodology is used to forecast the weather, such as temperature, precipitation, and humidity.
  
- Industrial forecasting: The Box-Jenkins methodology is used to forecast product demand, inventory levels, and other industrial indicators.

For this project, the methodology for forecasting the stock prices of companies listed on a stock exchange is used.

The purpose of this paper is to present a statistical tool for univariant analysis for the prediction of a company that trades in a continuous market and the target price of the company, that is, its stock. The application of this model is useful for the valuation of stocks and companies, as well as the prediction of volatility. Its only disadvantage is that, since it is a model that takes into account a single variable, there is no possibility of relating it to other variables that affect the value of a company's shares. However, this model is still very useful when a short-term prediction is desired due to its statistical application, which makes it a very useful model for making predictions of stock market values.

The difference between a process and a model. A process is what is real, of which the generating mechanism is unknown. A model is the imitation or representation of the process (Pankratz 1983).

Processes are classified as stationary and non-stationary. Stationary processes do not present systematic changes in the mean or variance, that is, they are constant over time. This means that a stationary process is stable, while non-stationary processes do present changes in the mean and variance. This means that the series is unstable over time.

$ARIMA$: can be applied to discrete (integer) and continuous (fractional) data; they require a minimum of 50 observations (Box-Jenkins); they are characterized by their slowly decaying autocorrelation function (ACF); it is assumed that the random disturbances present in the series are independent of each other, there is no correlation and therefore no pattern that can be modeled; it is a model developed with combinations of autoregressive (AR), integrated (I), and moving average (MA) processes.

The ACF, also known as the Autocorrelation Function, measures the correlation between two variables separated by k periods. It measures the degree of linear association that exists between two variables of the same stochastic process. It identifies the Moving Average (MA) process and provides information about its order q.

The Partial Autocorrelation Function (PACF) measures the correlation between two variables separated by k periods, while ignoring the dependence created by the intermediate lags between them. It measures the autocorrelation that exists between two variables k periods, discounting the possible effects due to intermediate variables. The PACF can be used to identify the Autoregressive (AR) process and to determine its order p.

DFA, also known as the Augmented Dickey-Fuller test, is a statistical test for unit roots. It allows to detect the presence or absence of unit roots and determines the behavior of the stochastic process.

Akaike, allows us to identify a model in a simpler and more automatic way. It is the Akaike Information Criterion (AIC), the solution is to choose the minimum AIC information criterion as the loss function (Specification Criteria). It allows us to interpret the distance between two distributions based on the log-likelihood of a model. It is to find a loss function that, when minimized, allows us to obtain the model that best fits the data.

The Non-Seasonal Box-Jenkins Methodology is the most widely used and known methodology proposed by G.E.P. Box and G.M. Jenkins in the 1970s in their book Time Series Analysis: Forecastingand Control, used for the prediction of economic variables. This is because during that period, when there was stagflation, structural econometric models were unable to provide an explanatory capacity. This methodology is based on five main stages: 
1. Stationarity
2. Identification
3. Estimation
4. Evaluation
5. Forecasting






