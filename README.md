Extracting and Visualizing Stock Data
Extracting essential data from a dataset and displaying it is a necessary part of data science; therefore individuals can make correct decisions based on the data. In this assignment, you will extract some stock data, you will then display this data in a graph.
Note:- If you are working in IBM Cloud Watson Studio, please replace the command for installing nbformat from !pip install nbformat==4.2.0 to simply !pip install nbformat
!pip install yfinance==0.1.67
!mamba install bs4==4.10.0 -y
!pip install nbformat==4.2.0
!mamba install html5lib==1.1 -y
[5]
import yfinance as yf
import pandas as pd
import requests
from bs4 import BeautifulSoup
import plotly.graph_objects as go
from plotly.subplots import make_subplots
In Python, you can ignore warnings using the warnings module. You can use the filterwarnings function to filter or ignore specific warning messages or categories.

[9]
import warnings
# Ignore all warnings
warnings.filterwarnings("ignore", category=FutureWarning)
In this section, we define the function make_graph. You don't have to know how the function works, you should only care about the inputs. It takes a dataframe with stock data (dataframe must contain Date and Close columns), a dataframe with revenue data (dataframe must contain Date and Revenue columns), and the name of the stock.

Question 1: Use yfinance to Extract Stock Data
Using the Ticker function enter the ticker symbol of the stock we want to extract data on to create a ticker object. The stock is Tesla and its ticker symbol is TSLA.

[11]
Tesla=yf.Ticker('TSLA')
Using the ticker object and the function history extract stock information and save it in a dataframe named tesla_data. Set the period parameter to max so we get information for the maximum amount of time.

[12]
tesla_data=Tesla.history(period='max')
[13]
tesla_data.reset_index(inplace=True)
tesla_data.head(5)

[14]
print(type(tesla_data))
<class 'pandas.core.frame.DataFrame'>

Question 2: Use Webscraping to Extract Tesla Revenue Data
Use the requests library to download the webpage https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/revenue.htm Save the text of the response as a variable named html_data.

[15]
url='https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/revenue.htm'
[16]
html_data=requests.get(url).text
Parse the html data using beautiful_soup.

[17]
soup=BeautifulSoup(html_data,"html5lib")
Using BeautifulSoup or the read_html function extract the table with Tesla Revenue and store it into a dataframe named tesla_revenue. The dataframe should have columns Date and Revenue.

Click here if you need help locating the table
[18]
tesla_revenue=pd.DataFrame(columns=['Date','Revenue'])
[19]
for row in soup.find_all('tbody')[1].find_all('tr'):
    col=row.find_all('td')
    date=col[0].text
    revenue=col[1].text
    tesla_revenue=tesla_revenue.append({'Date':date,'Revenue':revenue},ignore_index=True)
Execute the following line to remove the comma and dollar sign from the Revenue column.

[20]
tesla_revenue["Revenue"] = tesla_revenue['Revenue'].str.replace(',|\$',"")
Execute the following lines to remove an null or empty strings in the Revenue column.

[21]
tesla_revenue.dropna(inplace=True)

tesla_revenue = tesla_revenue[tesla_revenue['Revenue'] != ""]
Display the last 5 row of the tesla_revenue dataframe using the tail function. Take a screenshot of the results.

[22]
tesla_revenue.tail(5)

Question 3: Use yfinance to Extract Stock Data
Using the Ticker function enter the ticker symbol of the stock we want to extract data on to create a ticker object. The stock is GameStop and its ticker symbol is GME.

[23]
GameStop=yf.Ticker('GME')
Using the ticker object and the function history extract stock information and save it in a dataframe named gme_data. Set the period parameter to max so we get information for the maximum amount of time.

[24]
gme_data=GameStop.history(period='Max')
Reset the index using the reset_index(inplace=True) function on the gme_data DataFrame and display the first five rows of the gme_data dataframe using the head function. Take a screenshot of the results and code from the beginning of Question 3 to the results below.

[26]
gme_data.reset_index(inplace=True)
gme_data.head(5)

Question 4: Use Webscraping to Extract GME Revenue Data
[27]
url='https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/stock.html'
Parse the html data using beautiful_soup.

[28]
html_data=requests.get(url).text
[29]
soup=BeautifulSoup(html_data,'html.parser')
Using BeautifulSoup or the read_html function extract the table with GameStop Revenue and store it into a dataframe named gme_revenue. The dataframe should have columns Date and Revenue. Make sure the comma and dollar sign is removed from the Revenue column using a method similar to what you did in Question 2.

Click here if you need help locating the table
[30]
gme_revenue=pd.read_html(url)[1]
[31]
gme_revenue=gme_revenue.rename(columns={'GameStop Quarterly Revenue(Millions of US $)':'Date','GameStop Quarterly Revenue(Millions of US $).1':'Revenue'})
[32]
gme_revenue["Revenue"] = gme_revenue['Revenue'].str.replace(',|\$',"")
[33]
gme_revenue.dropna(inplace=True)
gme_revenue = gme_revenue[gme_revenue['Revenue'] != ""]
[34]
gme_revenue.tail(5)

Question 5: Plot Tesla Stock Graph
Use the make_graph function to graph the Tesla Stock Data, also provide a title for the graph. The structure to call the make_graph function is make_graph(tesla_data, tesla_revenue, 'Tesla'). Note the graph will only show data upto June 2021.

[52]
def make_graph(tesla_data, tesla_revenue,stock):
    fig = make_subplots(rows=2, cols=1, shared_xaxes=True, subplot_titles=("Historical Share Price", "Historical Revenue"), vertical_spacing = .3)
    tesla_data_specific = tesla_data[tesla_data['Date'] < '2021-07']
    tesla_revenue_specific = tesla_revenue[tesla_revenue['Date'] < '2021-07']
    fig.add_trace(go.Scatter(x=pd.to_datetime(tesla_data_specific.Date, infer_datetime_format=True), y=tesla_data_specific.Close.astype("float"), name="Share Price"), row=1, col=1)
    fig.add_trace(go.Scatter(x=pd.to_datetime(tesla_revenue_specific.Date, infer_datetime_format=True), y=tesla_revenue_specific.Revenue.astype("float"), name="Revenue"), row=2, col=1)
    fig.update_xaxes(title_text="Date", row=1, col=1)
    fig.update_xaxes(title_text="Date", row=2, col=1)
    fig.update_yaxes(title_text="Price ($US)", row=1, col=1)
    fig.update_yaxes(title_text="Revenue ($US Millions)", row=2, col=1)
    fig.update_layout(showlegend=False,
    height=900,
    title=stock,
    xaxis_rangeslider_visible=True)
    fig.show()
[53]
make_graph(tesla_data,tesla_revenue,'Tesla')

Question 6: Plot GameStop Stock Graph
Use the make_graph function to graph the GameStop Stock Data, also provide a title for the graph. The structure to call the make_graph function is make_graph(gme_data, gme_revenue, 'GameStop'). Note the graph will only show data upto June 2021.

[2]
def make_graph(gme_data, gme_revenue,stock):
    fig = make_subplots(rows=2, cols=1, shared_xaxes=True, subplot_titles=("Historical Share Price", "Historical Revenue"), vertical_spacing = .3)
    gme_data_specific = gme_data[gme_data['Date'] < '2021-07']
    gme_revenue_specific = gme_revenue[gme_revenue['Date'] < '2021-07']
    fig.add_trace(go.Scatter(x=pd.to_datetime(gme_data_specific.Date, infer_datetime_format=True), y=gme_data_specific.Close.astype("float"), name="Share Price"), row=1, col=1)
    fig.add_trace(go.Scatter(x=pd.to_datetime(gme_revenue_specific.Date, infer_datetime_format=True), y=gme_revenue_specific.Revenue.astype("float"), name="Revenue"), row=2, col=1)
    fig.update_xaxes(title_text="Date", row=1, col=1)
    fig.update_xaxes(title_text="Date", row=2, col=1)
    fig.update_yaxes(title_text="Price ($US)", row=1, col=1)
    fig.update_yaxes(title_text="Revenue ($US Millions)", row=2, col=1)
    fig.update_layout(showlegend=False,
    height=900,
    title=stock,
    xaxis_rangeslider_visible=True)
    fig.show()
[3]
make_graph(gme_data,gme_revenue,'GameStop')
