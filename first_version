from binance.client import Client
import pandas as pd
import time
import numpy as np

# Renseignez vos API Key et Secret
API_KEY = 'YOUR_API_KEY'
API_SECRET = 'YOUR_API_SECRET'

# Initialiser le client Binance
client = Client(API_KEY, API_SECRET)

# Configuration du bot
PAIR = "BTCUSDT"  # Pair de trading, ici Bitcoin/USDT
TRADE_QUANTITY = 0.001  # Quantité de crypto à trader
TIME_INTERVAL = "1m"  # Intervalle de temps des bougies ("1m", "5m", "1h", etc.)
SMA_PERIOD = 10  # Période pour la moyenne mobile simple

def get_historical_data(pair, interval, lookback):
    """Récupère les données historiques de la crypto"""
    klines = client.get_klines(symbol=pair, interval=interval, limit=lookback)
    df = pd.DataFrame(klines, columns=[
        "timestamp", "open", "high", "low", "close", "volume", "close_time",
        "quote_asset_volume", "number_of_trades", "taker_buy_base", "taker_buy_quote", "ignore"
    ])
    df["close"] = df["close"].astype(float)
    return df[["timestamp", "close"]]

def calculate_sma(data, period):
    """Calcule la moyenne mobile simple"""
    return data["close"].rolling(window=period).mean()

def place_order(order_type, quantity):
    """Place un ordre de marché"""
    try:
        if order_type == "BUY":
            order = client.order_market_buy(
                symbol=PAIR,
                quantity=quantity
            )
        elif order_type == "SELL":
            order = client.order_market_sell(
                symbol=PAIR,
                quantity=quantity
            )
        print(f"Ordre {order_type} passé avec succès : {order}")
    except Exception as e:
        print(f"Erreur lors du passage de l'ordre : {e}")

def trading_bot():
    """Exécution du bot de trading"""
    print("Démarrage du bot...")
    while True:
        try:
            # Récupérer les données historiques
            data = get_historical_data(PAIR, TIME_INTERVAL, SMA_PERIOD + 1)
            
            # Calculer la SMA
            data["SMA"] = calculate_sma(data, SMA_PERIOD)
            
            # Vérifier les conditions d'achat/vente
            last_price = data["close"].iloc[-1]
            last_sma = data["SMA"].iloc[-1]

            print(f"Prix actuel : {last_price}, SMA : {last_sma}")

            # Acheter si le prix est en dessous de la SMA
            if last_price < last_sma:
                print("Condition d'achat détectée.")
                place_order("BUY", TRADE_QUANTITY)

            # Vendre si le prix est au-dessus de la SMA
            elif last_price > last_sma:
                print("Condition de vente détectée.")
                place_order("SELL", TRADE_QUANTITY)

            # Attendre avant la prochaine itération
            time.sleep(60)
        except Exception as e:
            print(f"Erreur dans le bot : {e}")
            time.sleep(60)

if __name__ == "__main__":
    trading_bot()
