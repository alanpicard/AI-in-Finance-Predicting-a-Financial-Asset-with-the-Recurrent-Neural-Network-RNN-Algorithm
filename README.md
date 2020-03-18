{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
   
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "###Applying the RNN Algorithm to ",
    "Predict the Direction of the Price of a Financial Asset"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "###Author: Alan Picard"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "This project shows how to implement the Recurrent Neural Network, a popular AI algorithm, to predict the direction of the price/value of a financial asset, in this case the S&P500. "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Disclaimer: The purpose of this article is solely informational and educational in order to provide a basic introduction on how Artiﬁcial Intelligence can be applied in ﬁnance. As with any ﬁnancial assets and trading strategies, the model and results presented in this project do not guarantee future performance. The author, hence, will not bear any responsability for the outcomes obtained if one desires to implement the model. Moreover, the strategy exhibited below does not account for trading fees."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "###1. Retrieving Financial Data" 
    
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "The first step is to retrieve historical data of the asset of interest. Many approaches are possible to obtain the historical prices of any financial asset from the web. This project will use Yahoo Finance website to get past performance of the S&P500. Please check the other project on my github account to learn how you can retrieve historical financial data for any currency, stock price, fixed income instrument, commodity, economic data (e.g. US GDP) and world indices."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 67,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Importing the libraries\n",
    "import numpy as np\n",
    "import pandas as pd\n",
    "import matplotlib.pyplot as mp\n",
    "from pylab import plt\n",
    "plt.style.use('seaborn')\n",
    "%matplotlib inline\n",
    "import datetime\n",
    "import matplotlib.pyplot as plt\n",
    "from pandas_datareader import data as web # library to retrieve financial data from websites such as Yahoo Finance               "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 68,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Enter the symbol: ^GSPC\n",
      "Enter the start of the sample period (yyyy-mm-dd): 2015-01-01\n",
      "Enter the end of the sample period (yyyy-mm-dd): 2020-01-31\n"
     ]
    },
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>High</th>\n",
       "      <th>Low</th>\n",
       "      <th>Open</th>\n",
       "      <th>Close</th>\n",
       "      <th>Volume</th>\n",
       "      <th>Adj Close</th>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>Date</th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "      <th></th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>2015-01-02</th>\n",
       "      <td>2072.360107</td>\n",
       "      <td>2046.040039</td>\n",
       "      <td>2058.899902</td>\n",
       "      <td>2058.199951</td>\n",
       "      <td>2708700000</td>\n",
       "      <td>2058.199951</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2015-01-05</th>\n",
       "      <td>2054.439941</td>\n",
       "      <td>2017.339966</td>\n",
       "      <td>2054.439941</td>\n",
       "      <td>2020.579956</td>\n",
       "      <td>3799120000</td>\n",
       "      <td>2020.579956</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2015-01-06</th>\n",
       "      <td>2030.250000</td>\n",
       "      <td>1992.439941</td>\n",
       "      <td>2022.150024</td>\n",
       "      <td>2002.609985</td>\n",
       "      <td>4460110000</td>\n",
       "      <td>2002.609985</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2015-01-07</th>\n",
       "      <td>2029.609985</td>\n",
       "      <td>2005.550049</td>\n",
       "      <td>2005.550049</td>\n",
       "      <td>2025.900024</td>\n",
       "      <td>3805480000</td>\n",
       "      <td>2025.900024</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2015-01-08</th>\n",
       "      <td>2064.080078</td>\n",
       "      <td>2030.609985</td>\n",
       "      <td>2030.609985</td>\n",
       "      <td>2062.139893</td>\n",
       "      <td>3934010000</td>\n",
       "      <td>2062.139893</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>...</th>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2020-01-27</th>\n",
       "      <td>3258.850098</td>\n",
       "      <td>3234.500000</td>\n",
       "      <td>3247.159912</td>\n",
       "      <td>3243.629883</td>\n",
       "      <td>3823100000</td>\n",
       "      <td>3243.629883</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2020-01-28</th>\n",
       "      <td>3285.780029</td>\n",
       "      <td>3253.219971</td>\n",
       "      <td>3255.350098</td>\n",
       "      <td>3276.239990</td>\n",
       "      <td>3526720000</td>\n",
       "      <td>3276.239990</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2020-01-29</th>\n",
       "      <td>3293.469971</td>\n",
       "      <td>3271.889893</td>\n",
       "      <td>3289.459961</td>\n",
       "      <td>3273.399902</td>\n",
       "      <td>3584500000</td>\n",
       "      <td>3273.399902</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2020-01-30</th>\n",
       "      <td>3285.909912</td>\n",
       "      <td>3242.800049</td>\n",
       "      <td>3256.449951</td>\n",
       "      <td>3283.659912</td>\n",
       "      <td>3787250000</td>\n",
       "      <td>3283.659912</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2020-01-31</th>\n",
       "      <td>3282.330078</td>\n",
       "      <td>3214.679932</td>\n",
       "      <td>3282.330078</td>\n",
       "      <td>3225.520020</td>\n",
       "      <td>4527830000</td>\n",
       "      <td>3225.520020</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "<p>1279 rows × 6 columns</p>\n",
       "</div>"
      ],
      "text/plain": [
       "                   High          Low         Open        Close      Volume  \\\n",
       "Date                                                                         \n",
       "2015-01-02  2072.360107  2046.040039  2058.899902  2058.199951  2708700000   \n",
       "2015-01-05  2054.439941  2017.339966  2054.439941  2020.579956  3799120000   \n",
       "2015-01-06  2030.250000  1992.439941  2022.150024  2002.609985  4460110000   \n",
       "2015-01-07  2029.609985  2005.550049  2005.550049  2025.900024  3805480000   \n",
       "2015-01-08  2064.080078  2030.609985  2030.609985  2062.139893  3934010000   \n",
       "...                 ...          ...          ...          ...         ...   \n",
       "2020-01-27  3258.850098  3234.500000  3247.159912  3243.629883  3823100000   \n",
       "2020-01-28  3285.780029  3253.219971  3255.350098  3276.239990  3526720000   \n",
       "2020-01-29  3293.469971  3271.889893  3289.459961  3273.399902  3584500000   \n",
       "2020-01-30  3285.909912  3242.800049  3256.449951  3283.659912  3787250000   \n",
       "2020-01-31  3282.330078  3214.679932  3282.330078  3225.520020  4527830000   \n",
       "\n",
       "              Adj Close  \n",
       "Date                     \n",
       "2015-01-02  2058.199951  \n",
       "2015-01-05  2020.579956  \n",
       "2015-01-06  2002.609985  \n",
       "2015-01-07  2025.900024  \n",
       "2015-01-08  2062.139893  \n",
       "...                 ...  \n",
       "2020-01-27  3243.629883  \n",
       "2020-01-28  3276.239990  \n",
       "2020-01-29  3273.399902  \n",
       "2020-01-30  3283.659912  \n",
       "2020-01-31  3225.520020  \n",
       "\n",
       "[1279 rows x 6 columns]"
      ]
     },
     "execution_count": 68,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# ticker - you can enter any stock symbol e.g. AAPL, GE, MSFT. The symbol for the S&P500 is ^GSPC.\n",
    "ticker = str(input(\"Enter the symbol: \"))\n",
    "\n",
    "# start_date\n",
    "start_date = str(input(\"Enter the start of the sample period (yyyy-mm-dd): \"))\n",
    "\n",
    "# end_date\n",
    "end_date = str(input(\"Enter the end of the sample period (yyyy-mm-dd): \"))\n",
    "\n",
    "dataSP500 = web.DataReader(ticker, data_source = 'yahoo', start = start_date, end  = end_date)\n",
    "dataSP500"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Historical prices data for the S&P500, from January 2015 to January 2020, is uploaded into a pandas dataframe giving 1279 trading days. ",
    "We can round up the number of decimals to 2 and then select the column of interest that is Open Price, needed for the RNN algorithm."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 69,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "21"
      ]
     },
     "execution_count": 69,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "dataSP500.reset_index(inplace = True, drop = False) \n",
    "dataSP500['Year'] = pd.DatetimeIndex(dataSP500['Date']).year\n",
    "dataSP500['Month'] = pd.DatetimeIndex(dataSP500['Date']).month\n",
    "\n",
    "# To get the number of trading days in January 2020 since this is the sample  we try to predict. # Any other days, months \n",
    "# and years can be selected \n",
    "nb_days_to_pred = len(dataSP500[(dataSP500['Year'] == 2020) & (dataSP500['Month'] == 1)]) \n",
    "nb_days_to_pred"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 70,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>Open</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>2058.90</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>2054.44</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>2022.15</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>2005.55</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>2030.61</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>...</th>\n",
       "      <td>...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1274</th>\n",
       "      <td>3247.16</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1275</th>\n",
       "      <td>3255.35</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1276</th>\n",
       "      <td>3289.46</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1277</th>\n",
       "      <td>3256.45</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1278</th>\n",
       "      <td>3282.33</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "<p>1279 rows × 1 columns</p>\n",
       "</div>"
      ],
      "text/plain": [
       "         Open\n",
       "0     2058.90\n",
       "1     2054.44\n",
       "2     2022.15\n",
       "3     2005.55\n",
       "4     2030.61\n",
       "...       ...\n",
       "1274  3247.16\n",
       "1275  3255.35\n",
       "1276  3289.46\n",
       "1277  3256.45\n",
       "1278  3282.33\n",
       "\n",
       "[1279 rows x 1 columns]"
      ]
     },
     "execution_count": 70,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "dataSP500 = dataSP500[['Open']]                          # selects the columns of interest \n",
    "dataSP500.reset_index(inplace = True, drop = True)       # sets ordinal numbers as the column index\n",
    "dataSP500 = dataSP500.round(2)                           # rounds the number of decimals to 2\n",
    "dataSP500"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 2. Data Preprocessing"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 73,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Number of trading days in the training set:  1258\n",
      "Number of trading days in the test set:  21\n"
     ]
    }
   ],
   "source": [
    "# Splitting the data between the training set and the test set\n",
    "# The goal is to predict the opening prices for the month of January 2020 (test set) using data from January 2015 to \n",
    "# December 2019 (training set).\n",
    "# We, hence, need to split the data between the training set and test set.\n",
    "\n",
    "# from sklearn.model_selection import train_test_split\n",
    "# train_set, test_Set = train_test_split(dataSP500, test_size = ((len(dataSP500) - 21) / len(dataSP500)), random_state = 0)\n",
    "train_set = dataSP500.iloc[:len(dataSP500) - nb_days_to_pred]\n",
    "test_set = dataSP500.iloc[len(dataSP500) - nb_days_to_pred :]\n",
    "print(\"Number of trading days in the training set: \", len(train_set))\n",
    "print(\"Number of trading days in the test set: \",len(test_set))"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 74,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Feature Scaling\n",
    "from sklearn.preprocessing import MinMaxScaler\n",
    "sc = MinMaxScaler(feature_range = (0, 1))\n",
    "training_set_scaled = sc.fit_transform(train_set)\n",
    "\n",
    "#\n",
    "# Creating a data structure with 60 timesteps and 1 output\n",
    "X_train = []\n",
    "y_train = []\n",
    "for i in range(60, len(train_set)):\n",
    "    X_train.append(training_set_scaled[i-60:i, 0])   # RNN algorithm is trained using 60 trading days \n",
    "    y_train.append(training_set_scaled[i, 0])        # RNN algorithm is trained to predict the 60th opening trading day\n",
    "X_train, y_train = np.array(X_train), np.array(y_train)\n",
    "\n",
    "# Reshaping\n",
    "X_train = np.reshape(X_train, (X_train.shape[0], X_train.shape[1], 1))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 3. Building the RNN Model "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 75,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Epoch 1/100\n",
      "1198/1198 [==============================] - 6s 5ms/step - loss: 0.0459\n",
      "Epoch 2/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0059\n",
      "Epoch 3/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0052\n",
      "Epoch 4/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0044\n",
      "Epoch 5/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0037\n",
      "Epoch 6/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0040\n",
      "Epoch 7/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0038\n",
      "Epoch 8/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0040\n",
      "Epoch 9/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0041\n",
      "Epoch 10/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0036\n",
      "Epoch 11/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0034\n",
      "Epoch 12/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0036\n",
      "Epoch 13/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0035\n",
      "Epoch 14/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0031\n",
      "Epoch 15/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0031\n",
      "Epoch 16/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0032\n",
      "Epoch 17/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0032\n",
      "Epoch 18/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0031\n",
      "Epoch 19/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0027\n",
      "Epoch 20/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0030\n",
      "Epoch 21/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0028\n",
      "Epoch 22/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0029\n",
      "Epoch 23/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0026\n",
      "Epoch 24/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0027\n",
      "Epoch 25/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0029\n",
      "Epoch 26/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0028\n",
      "Epoch 27/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0026\n",
      "Epoch 28/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0029\n",
      "Epoch 29/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0026\n",
      "Epoch 30/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0025\n",
      "Epoch 31/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0024\n",
      "Epoch 32/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0027\n",
      "Epoch 33/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0027\n",
      "Epoch 34/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0026\n",
      "Epoch 35/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0022\n",
      "Epoch 36/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0023\n",
      "Epoch 37/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0025\n",
      "Epoch 38/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0022\n",
      "Epoch 39/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0022\n",
      "Epoch 40/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0019\n",
      "Epoch 41/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0021\n",
      "Epoch 42/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0026\n",
      "Epoch 43/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0022\n",
      "Epoch 44/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0020\n",
      "Epoch 45/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0021\n",
      "Epoch 46/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0022\n",
      "Epoch 47/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0022\n",
      "Epoch 48/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0020\n",
      "Epoch 49/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0019\n",
      "Epoch 50/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0019\n",
      "Epoch 51/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0020\n",
      "Epoch 52/100\n",
      "1198/1198 [==============================] - 4s 3ms/step - loss: 0.0018\n",
      "Epoch 53/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0019\n",
      "Epoch 54/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0018\n",
      "Epoch 55/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0018\n",
      "Epoch 56/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0019\n",
      "Epoch 57/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0020\n",
      "Epoch 58/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0016\n",
      "Epoch 59/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0023\n",
      "Epoch 60/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0018\n",
      "Epoch 61/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0017\n",
      "Epoch 62/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0016\n",
      "Epoch 63/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0015\n",
      "Epoch 64/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0015\n",
      "Epoch 65/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0017\n",
      "Epoch 66/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0015\n",
      "Epoch 67/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0016\n",
      "Epoch 68/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0016\n",
      "Epoch 69/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0014\n",
      "Epoch 70/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0015\n",
      "Epoch 71/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0016\n",
      "Epoch 72/100\n",
      "1198/1198 [==============================] - 6s 5ms/step - loss: 0.0017\n",
      "Epoch 73/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0015\n",
      "Epoch 74/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0016\n",
      "Epoch 75/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0014\n",
      "Epoch 76/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0014\n",
      "Epoch 77/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0013\n",
      "Epoch 78/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0014\n",
      "Epoch 79/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0015\n",
      "Epoch 80/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0013\n",
      "Epoch 81/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0013\n",
      "Epoch 82/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0013\n",
      "Epoch 83/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0013\n",
      "Epoch 84/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0013\n",
      "Epoch 85/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0013\n",
      "Epoch 86/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0014\n",
      "Epoch 87/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0013\n",
      "Epoch 88/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0014\n",
      "Epoch 89/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0013\n",
      "Epoch 90/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0013\n",
      "Epoch 91/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0011\n",
      "Epoch 92/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0014\n",
      "Epoch 93/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0013\n",
      "Epoch 94/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0014\n",
      "Epoch 95/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0012\n",
      "Epoch 96/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0011\n",
      "Epoch 97/100\n"
     ]
    },
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0013\n",
      "Epoch 98/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0012\n",
      "Epoch 99/100\n",
      "1198/1198 [==============================] - 4s 4ms/step - loss: 0.0012\n",
      "Epoch 100/100\n",
      "1198/1198 [==============================] - 5s 4ms/step - loss: 0.0011\n"
     ]
    },
    {
     "data": {
      "text/plain": [
       "<keras.callbacks.History at 0x284b48c0608>"
      ]
     },
     "execution_count": 75,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "# Importing the Keras libraries and packages\n",
    "from keras.models import Sequential\n",
    "from keras.layers import Dense\n",
    "from keras.layers import LSTM\n",
    "from keras.layers import Dropout\n",
    "\n",
    "# Initialising the RNN\n",
    "regressor = Sequential()\n",
    "\n",
    "# Adding the first LSTM layer and some Dropout regularisation\n",
    "regressor.add(LSTM(units = 50, return_sequences = True, input_shape = (X_train.shape[1], 1)))\n",
    "regressor.add(Dropout(0.2))\n",
    "\n",
    "# Adding a second LSTM layer and some Dropout regularisation\n",
    "regressor.add(LSTM(units = 50, return_sequences = True))\n",
    "regressor.add(Dropout(0.2))\n",
    "\n",
    "# Adding a third LSTM layer and some Dropout regularisation\n",
    "regressor.add(LSTM(units = 50, return_sequences = True))\n",
    "regressor.add(Dropout(0.2))\n",
    "\n",
    "# Adding a fourth LSTM layer and some Dropout regularisation\n",
    "regressor.add(LSTM(units = 50))\n",
    "regressor.add(Dropout(0.2))\n",
    "\n",
    "# Adding the output layer\n",
    "regressor.add(Dense(units = 1))\n",
    "\n",
    "# Compiling the RNN\n",
    "regressor.compile(optimizer = 'adam', loss = 'mean_squared_error')\n",
    "\n",
    "# Fitting the RNN to the Training set\n",
    "regressor.fit(X_train, y_train, epochs = 100, batch_size = 32)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 4. Making the Predictions and Visualising the Results "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "The RNN built in this project is a regressor type model because a continuous outcome is being predicted (the S&P500 value or any financial instrument). For Regression, the usual approach to evaluate the model performance is with the metric called RMSE (Root Mean Squared Error). It is calculated as the root of the mean of the squared differences between the predictions and the real values.",
    "\n",
    "However for this specific Index Value Prediction problem, evaluating the model with the RMSE does not make much sense, since it is the directions taken by the predictions that are evaluated, rather than the closeness of their values to the real level/value. \n",
    "Checking if the predictions follow the same directions as the real S&P500 levels and not whether the  predictions are close the real stock price is critical in the application of the RNN algorithm."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 76,
   "metadata": {},
   "outputs": [],
   "source": [
    "# Getting the real value of the S&P500 for January 2020\n",
    "real_valuesSP500 = np.array(test_set)\n",
    "\n",
    "# Getting the predicted stock price of 2017\n",
    "dataset_total = pd.concat((train_set, test_set), axis = 0)\n",
    "inputs = dataset_total[len(dataset_total) - len(test_set) - 60:].values\n",
    "inputs = inputs.reshape(-1,1)\n",
    "inputs = sc.transform(inputs)\n",
    "X_test = []\n",
    "for i in range(60, 60 + len(real_valuesSP500)):\n",
    "    X_test.append(inputs[i-60:i, 0])\n",
    "X_test = np.array(X_test)\n",
    "X_test = np.reshape(X_test, (X_test.shape[0], X_test.shape[1], 1))\n",
    "predicted_SP500value = regressor.predict(X_test)\n",
    "predicted_SP500value = sc.inverse_transform(predicted_SP500value)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 77,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 432x288 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "# Visualising the results\n",
    "\n",
    "plt.plot(real_valuesSP500, color = 'red', label = 'Real S&P500 Index')\n",
    "plt.plot(predicted_SP500value, color = 'blue', label = 'Predicted S&P500 Value')\n",
    "plt.title('S&P500 Prediction')\n",
    "plt.xlabel('Time')\n",
    "plt.ylabel('S&P500')\n",
    "plt.legend()\n",
    "plt.show()\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### 5. Implementing the Strategy  "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "A trading strategy based on the RNN algorithm can then be implemented where the S&P500 is bought at the current day closing time \n",
    "if it is predicted that the next day opening will be higher than current day opening and short sell the S&P500 at the closing trading if it is predicted that next day opening will be lower. "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 78,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Real Direction of S&P500 Opening (Higher-Lower): \n",
      "[-1] [-1] [1] [-1] [1] [1] [-1] [1] [-1] [1] [1] [-1] [1] [-1] [1] [-1] [1] [1] [-1] [1]\n",
      "Predicted Direction of S&P500 Opening (Higher-Lower): \n",
      "[-1] [-1] [-1] [1] [1] [1] [1] [1] [1] [1] [1] [1] [1] [1] [1] [1] [-1] [-1] [-1] [-1]\n"
     ]
    }
   ],
   "source": [
    "#  \n",
    "higher_lower_real = np.greater_equal(real_valuesSP500[1:], real_valuesSP500[0:len(real_valuesSP500) -1], ).astype(int)\n",
    "higher_lower_predicted = np.greater_equal(predicted_SP500value[1:], predicted_SP500value[0:len(predicted_SP500value) -1], ).astype(int)\n",
    "\n",
    "# replacing zeros by -1 to refer to the lower opening \n",
    "higher_lower_real[higher_lower_real == 0 ] = -1\n",
    "higher_lower_predicted[higher_lower_predicted == 0 ] = -1\n",
    "\n",
    "# the  + ones (- ones) indicate if the real and predicted value of next day opening was higher (lower) \n",
    "# hence if higher_lower_predicted indicates + one (- one) it means that the S&P500 should be bought (sold)\n",
    "print('Real Direction of S&P500 Opening (Higher-Lower): ')\n",
    "print(*higher_lower_real, sep=' ')\n",
    "print('Predicted Direction of S&P500 Opening (Higher-Lower): ' )\n",
    "print(' '.join(str(value) for value in higher_lower_predicted))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "The Buy-and-Hold strategy of buying for 1000$ of the S&P500 and holding it during the month of January 2020 would have given a return of 1.29% or a profit of 12.90 dollars:"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "$$ 1000 * (1+ (3286 - 3244)/ 3244)) = 1012.90$$"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "Implementing the strategy based on the RNN model would give a profit of 68.25$ or 6.82% return. Note that this return is before trading fees which will almost surely impair the final return on the RNN strategy."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 79,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Implementing the strategy based on the RNN model would give an outcome of: 1068.25$\n"
     ]
    }
   ],
   "source": [
    "return_RNN = 1000\n",
    "for i in range(len(higher_lower_predicted) - 1):\n",
    "    return_RNN *= 1 + (higher_lower_predicted[i + 1] * (real_valuesSP500[i + 1] - real_valuesSP500[i])/real_valuesSP500[i])\n",
    "print('Implementing the strategy based on the RNN model would give an outcome of:% 5.2f$'  %(return_RNN[0]))"
   ]
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.7.6"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 2
}
