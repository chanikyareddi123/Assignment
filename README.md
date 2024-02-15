import requests
from bs4 import BeautifulSoup
import csv

def get_stock_info(ticker):
    url = f"https://finance.yahoo.com/quote/{ticker}"
    response = requests.get(url)
    soup = BeautifulSoup(response.text, 'html.parser')

    # Get stock price
    price = soup.find('div', {'class': 'D(ib) Mend(20px)'}).find_all('span')[0].text

    # Get company information
    company_info = soup.find_all('td', {'class': 'Ta(end) Fw(600) Lh(14px)'})
    industry = company_info[0].text
    sector = company_info[1].text
    employees = company_info[2].text

    return price, industry, sector, employees

def save_to_csv(data, filename):
    with open(filename, 'w', newline='') as csvfile:
        writer = csv.writer(csvfile)
        writer.writerow(['Ticker', 'Price', 'Industry', 'Sector', 'Employees'])
        for ticker, info in data.items():
            writer.writerow([ticker] + list(info))

def main():
    tickers = {
        'GOEV': 'Canoo'
    }
    stock_data = {}
    for ticker, company_name in tickers.items():
        try:
            price, industry, sector, employees = get_stock_info(ticker)
            stock_data[ticker] = (price, industry, sector, employees)
            print(f"Retrieved data for {company_name} (Ticker: {ticker})")
        except Exception as e:
            print(f"Failed to retrieve data for {company_name} (Ticker: {ticker}): {e}")

    save_to_csv(stock_data, 'canoo_stock_info.csv')


if __name__ == "__main__":
    main()
