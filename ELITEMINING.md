import React, { useState, useEffect } from 'react';
import { View, Text, TextInput, Button, StyleSheet, SafeAreaView, FlatList, Alert, Image, ScrollView } from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import axios from 'axios';

const Stack = createStackNavigator();

const App = () => {
  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteName="Login">
        <Stack.Screen name="Login" component={LoginScreen} />
        <Stack.Screen name="Register" component={RegisterScreen} />
        <Stack.Screen name="Home" component={HomeScreen} />
        <Stack.Screen name="Portfolio" component={PortfolioScreen} />
        <Stack.Screen name="Trade" component={TradeScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
};

// Login Screen
const LoginScreen = ({ navigation }) => {
  // ... (previous login screen code remains unchanged)
};

// Register Screen
const RegisterScreen = ({ navigation }) => {
  // ... (previous register screen code remains unchanged)
};

// Home Screen
const HomeScreen = ({ navigation }) => {
  const [prices, setPrices] = useState([]);

  useEffect(() => {
    const fetchPrices = async () => {
      try {
        const response = await axios.get('https://api.binance.com/api/v3/ticker/price', {
          params: {
            symbol: 'BTCUSDT,ETHUSDT'
          }
        });
        const formattedPrices = response.data.map(item => [item.symbol.slice(0, -4).toLowerCase(), { usd: parseFloat(item.price) }]);
        setPrices(formattedPrices);
      } catch (error) {
        console.error('Error fetching prices from Binance:', error);
      }
    };
    fetchPrices();
  }, []);

  return (
    <SafeAreaView style={styles.container}>
      <Image source={{uri: 'home_background.jpg'}} style={styles.backgroundImage} />
      <Text style={styles.title}>Welcome to ELITE-MINING STOCKS</Text>
      <FlatList 
        data={prices}
        keyExtractor={item => item[0]}
        renderItem={({ item }) => (
          <View style={styles.priceItem}>
            <Text>{item[0]}</Text>
            <Text>${item[1].usd.toFixed(2)}</Text>
          </View>
        )}
      />
      <Button title="View Portfolio" onPress={() => navigation.navigate('Portfolio')} />
      <Button title="Trade" onPress={() => navigation.navigate('Trade')} />
    </SafeAreaView>
  );
};

// Portfolio Screen
const PortfolioScreen = () => {
  // ... (previous portfolio screen code remains unchanged)
};

// Trade Screen
const TradeScreen = () => {
  const [symbol, setSymbol] = useState('BTCUSDT');
  const [quantity, setQuantity] = useState('');
  const [side, setSide] = useState('BUY'); // BUY or SELL
  const [errorMessage, setErrorMessage] = useState('');

  const handleTrade = async () => {
    if (!quantity) {
      setErrorMessage('Please enter a quantity');
      return;
    }

    // Mock API call to Binance for trading (replace with actual API usage)
    try {
      // Note: This is a simulation. In a real app, you would use the Binance API to place orders.
      // The actual API requires authentication and secure handling of API keys.
      const response = await axios.post('https://api.binance.com/api/v3/order/test', {
        symbol: symbol,
        side: side,
        type: 'MARKET',
        quantity: quantity
      });

      if (response.status === 200) {
        Alert.alert('Trade', `Your ${side === 'BUY' ? 'buy' : 'sell'} order for ${quantity} ${symbol} has been simulated.`, [
          { text: 'OK', onPress: () => console.log('Trade simulated') }
        ]);
        setErrorMessage('');
      } else {
        setErrorMessage('Trade failed');
      }
    } catch (error) {
      console.error('Error placing trade:', error);
      setErrorMessage('An error occurred while processing your trade');
    }
  };

  return (
    <SafeAreaView style={styles.container}>
      <Image source={{uri: 'trade_background.jpg'}} style={styles.backgroundImage} />
      <Text style={styles.title}>Trade</Text>
      {errorMessage ? <Text style={styles.errorText}>{errorMessage}</Text> : null}
      <ScrollView style={styles.scrollView}>
        <View style={styles.form}>
          <Text>Symbol:</Text>
          <TextInput 
            value={symbol}
            onChangeText={setSymbol}
            style={styles.input}
          />
          <Text>Quantity:</Text>
          <TextInput 
            value={quantity}
            onChangeText={setQuantity}
            keyboardType='numeric'
            style={styles.input}
          />
          <View style={styles.sideButtons}>
            <Button title="Buy" onPress={() => setSide('BUY')} color={side === 'BUY' ? 'green' : 'gray'} />
            <Button title="Sell" onPress={() => setSide('SELL')} color={side === 'SELL' ? 'red' : 'gray'} />
          </View>
          <Button title={`Place ${side} Order`} onPress={handleTrade} />
        </View>
      </ScrollView>
    </SafeAreaView>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    padding: 20,
  },
  input: {
    width: 200,
    height: 44,
    padding: 10,
    borderWidth: 1,
    borderColor: 'black',
    marginBottom: 10,
  },
  errorText: {
    color: 'red',
    marginBottom: 10,
  },
  title: {
    fontSize: 24,
    fontWeight: 'bold',
    marginBottom: 20,
  },
  backgroundImage: {
    position: 'absolute',
    top: 0,
    left: 0,
    width: '100%',
    height: '100%',
    opacity: 0.3,
  },
  priceItem: {
    padding: 10,
    borderBottomWidth: 1,
    borderBottomColor: '#ccc',
  },
  portfolioItem: {
    padding: 10,
    borderBottomWidth: 1,
    borderBottomColor: '#ccc',
    alignItems: 'center'
  },
  scrollView: {
    width: '100%',
  },
  form: {
    width: '100%',
  },
  sideButtons: {
    flexDirection: 'row',
    justifyContent: 'space-around',
    marginBottom: 10,
  }
});

export default App;
