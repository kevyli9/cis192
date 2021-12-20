Video walk-through: https://youtu.be/N692sHdEfW4  

Collaborators: none    

Note: I could not get my Poetry package to install correctly (I went to office hours), so I just ran pip freeze > requirements.txt  

Submission Requirements:  
* Class definition: models.SavedPortfolio  
	* magic methods: __str__ and __len__  
* First-party packages: json, io  
* Third-party packages: django, scipy, yfinance, pandas, numpy  

Routes  
* '/' (splash view): 
    * for logged-in users, displays: home page with a navigation bar to the different pages of the website  
    * for not logged-in users: displays site title with description, and a link to sign in or create an account  
* 'build': builds a portfolio of S&P500 companies that maximizes returns for a given volatility upper bound (input by the user) and train-test-split (% of the historical stock returns times series data used to build the optimization model)  	
* 'plot_port': builds cumulative stock returns charts for the training and test periods  
* 'login/': provides an option to sign up and an option to login (for existing users)  
* 'logout/': logs current user out and redirects to the login page    
* 'signup/': creates an account for the user and logs them in  
* 'save_portfolio/': saves a created portfolio to the database  
* 'delete': delete a selected portfolio  
* 'profile/': displays all portfolios saved by a given user  

Design considerations  
* Set-up:  
	* download_sp500_price.py: scrapes Wiki page for tickers of S&P500 companies, then uses yfinance package to download historical prices for each S&P500 company (as well as the SPY ETF), and merges all of this data into a single parquet file  
* Initialization:  
	* apps.py: loads S&P500 price parquet file into a dataframe and creates a dataframe of daily price returns. Contains 2 classes: CoreConfig (default) and Portfolio. Portfolio contains the logic for constructing an optimal portfolio given a volatility upper bound.  
* Models:  
    * SavedPortfolio: 4 fields (portfolio name, portfolio content, time stamp, and author) to represent a portfolio of stocks the user has saved. 'author' field is made a ForeignKey to a User object (many-to-one) enabling multiple portfolios to be associated with a given user. A portfolio's content is a json string representing a ticker-to-weight dictionary. 2 magic methods: __str__ and __len__ (number of stocks in the portfolio)    
* Views:  
    * splash: different templates are rendered depending on if the user is authenticated (index.html) or not (splash.html).   
    * index: renders home page (index.html)  
	* login: different templates are rendered depending on if the user is authenticated (index.html) or not (login.html). As such, if a user doesn't enter a username and password that match an existing account, they will have to try to log-in again or create a new account.  
    * logout: logs current user out and renders the login page  
    * signup: logs the user in if they succesfully create a new account and renders the home page such that they can immediately begin using the site.  
    * delete: deletes the selected portfolio   
    * build: Creates a portfolio object and associates it with the session username, storing it in a global dictionary. Then, calls max_return_with_limit_vol_port method to construct optimal portfolio given user constraints. Finally, renders the build page (port.html)   
    * plot_port: create a matplot figure with 2 cumulative returns plots (one for the training period, the other for the testing period).  
	* save_portfolio: saves the constructed portfolio's name, author, time stamp, and content (a ticker-to-weight dictionary that is converted into a json string), then redirects to the profile page    
	* profile: retrieves all SavedPortfolio objects associated with a given user, then renders the profile page (profile.html)   
  
Running the server:   
* after unzipping the submission zip file, cd to the top directory ('stocks')  
* run: python manage.py migrate  
* (optional since submission includes an already made data file) run: python3 download_sp500_price.py  
	* make sure your run-time environment does not block access to the Yahoo Finance website  
	* this will generate a parquet file called 'sp500_hist_price_20161101_20211101.parquet'  
* run: python manage.py runserver  
* go to localhost:8000 in your browser  