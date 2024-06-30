----------------------------------------------------------
  # System Requirements ⚙️ to Run a Fuel Node
----------------------------------------------------------  
- OS: Ubuntu 22 or 24
- Minimum: 2CPU, 4RAM, 30GB SSD
- Recommended: 8CPU, 12RAM, 100GB SSD
----------------------------------------------------------

💢 Step 1

```
sudo apt-get update && apt-get upgrade -y
sudo apt install wget curl
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs/ | sh
apt install rustup
```
💢 Step 2

```
curl https://install.fuel.network | sh
```

  #Press Y then Enter

💢 Step 3

```
source /root/.bashrc
fuelup toolchain install latest
fuelup --version
```

💢 Step 4

```
fuelup self update
```

💢 Step 5

```
fuelup toolchain install nightly
```

💢 Step 6

```
fuelup default nightly
```

💢 Step 7

```
fuelup show
```

💢 Step 8

```
forc wallet new
```

#You need to choose a password for yourself; the password will not be displayed as you type. Then press Enter, re-enter the password, and press Enter again. After that, you will be given recovery words that you need to save.

💢 Step 9

```
forc wallet account new
```

#You will be given a wallet address. You need to use this address (https://faucet-testnet.fuel.network) to request faucet funds for your wallet.

💢 Step 10

```
fuelup default
```

#Go to the Alchemy website and create an account. Then, in the Apps section, click on Create new app. In the Chain section, select Ethereum, and in the Network section, select Sepolia. Enter a name and description, and finally, click on Create app. Now, save the three sections from the API Key section: API Key, HTTPS, and WebSocket.

💢 Step 11

```
git clone https://github.com/fmsuicmc/metadata-fuel
```

💢 Step 12

```
fuelup toolchain install testnet
```

💢 Step 13

```
fuelup default testnet
fuel-core-keygen new --key-type peering
```

#In this section, copy the p2p key and store it in a safe place.

💢 Step 14

```
apt install screen
screen -S fuel
```

💢 Step 15


#Replace the ANY_SERVICE_NAME section with your desired name, the P2P_SECRET section with the corresponding p2p key, and the ETH_RPC_ENDPOINT section with the Rpc from the HTTPS section on the Alchemy website.

```
fuel-core run \
--service-name {ANY_SERVICE_NAME} \
--keypair {P2P_SECRET} \
--relayer {ETH_RPC_ENDPOINT} \
--ip 0.0.0.0 --port 4000 --peering-port 30333 \
--db-path ~/.testnet \
--snapshot $Home/root/metadata-fuel \
--utxo-validation --poa-instant false --enable-p2p \
--min-gas-price 1 --max-block-size 18874368 --max-transmit-size 18874368 \
--reserved-nodes /dns4/p2p-devnet.fuel.network/tcp/30333/p2p/16Uiu2HAm6pmJUedRFjennk4A8yWL6zCApHCuykzRRroqMjjxZ8o6,/dns4/p2p-devnet.fuel.network/tcp/30334/p2p/16Uiu2HAm8dBwTRzqazCMqQDdR8thMa7BKiW4ep2B4DoQQp6Qhyfd \
--sync-header-batch-size 100 \
--enable-relayer \
--relayer-v2-listening-contracts 0x01855B78C1f8868DE70e84507ec735983bf262dA \
--relayer-da-deploy-height 5827607 \
--relayer-log-page-size 2000
```

#Press Enter and use the Ctrl+A+D command to exit the screen.

#If the numbers are not zero in Alchemy, it means you have done it correctly.



🥳 Congrats You've run a node on Fuel.Network! 🥳 

----------------------------------------------------------
----------------------------------------------------------
----------------------------------------------------------
----------------------------------------------------------
----------------------------------------------------------

# 🌟Now we need to create a project and deploy a contract

💢 Step 1 (creating a project)

```
mkdir fuel-project
cd fuel-project
forc new counter-contract
```
💢 Step 2 (Editing contract)

```
nano counter-contract/src/main.sw
```
#Delete everything and Paste this code

```
contract;
 
storage {
    counter: u64 = 0,
}
 
abi Counter {
    #[storage(read, write)]
    fn increment();
 
    #[storage(read)]
    fn count() -> u64;
}
 
impl Counter for Contract {
    #[storage(read)]
    fn count() -> u64 {
        storage.counter.read()
    }
 
    #[storage(read, write)]
    fn increment() {
        let incremented = storage.counter.read() + 1;
        storage.counter.write(incremented);
    }
}
```
#Press crtl + X t , Press Y , Press Enter

```
cd counter-contract
forc build

```


#Deploy Contract , Enter password , Enter 1 , Enter y
```
forc deploy --testnet
```

#You get a Contract ID in your Terminal like this, Save it!
Contract counter-contract Deployed!

Network: https://testnet.fuel.network
Contract ID: 0x8342d413de2a678245d9ee39f020795800c7e6a4ac5ff7daae275f533dc05e08
Deployed in block: 0x4ea52b6652836c499e44b7e42f7c22d1ed1f03cf90a1d94cd0113b9023dfa636

💢 Step 3 (Checking Nodejs Version)


```
node --version
```
💢 Step 4 (Deleting old files)

```
sudo apt-get remove nodejs
sudo apt-get purge nodejs
sudo apt-get autoremove
sudo rm /etc/apt/keyrings/nodesource.gpg
sudo rm /etc/apt/sources.list.d/nodesource.list
```
💢 Step 5 (Installing Nodejs 18)

```
NODE_MAJOR=18
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg
sudo mkdir -p /etc/apt/keyrings
```
```
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg
```
```
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_${NODE_MAJOR}.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list
```
```
sudo apt-get update
sudo apt-get install -y nodejs
node --version
```
💢 Step 6 (Creating Dapp Frontend)

```
cd $HOME && cd fuel-project
npx create-react-app frontend --template typescript
```
💢 Step 7 (Installing fuels sdk)

```
ls
cd frontend
npm install fuels @fuels/react @fuels/connectors @tanstack/react-query
```
```
npm audit fix --force
```
💢 Step 8 (Generating Contract type)

```
npx fuels init --contracts ../counter-contract/ --output ./src/sway-api
npx fuels build
```

#Yout have to get this message:

Building Sway programs using source 'forc' binary
Generating types..
🎉  Build completed successfully!

#If you get Config file not found again do this:
```
npx fuels init --contracts ../counter-contract/ --output ./src/sway-api
```

💢 Step 9
```
npx fuels build
```

💢 Step 10 (Editing index)

```
nano src/index.tsx
```

💢 Step 11 (Delete everything and Paste this code)

```
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';
import { FuelProvider } from '@fuels/react';
import {
  defaultConnectors,
} from '@fuels/connectors';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';

const queryClient = new QueryClient();

const root = ReactDOM.createRoot(
  document.getElementById('root') as HTMLElement
);
root.render(
  <React.StrictMode>
    <QueryClientProvider client={queryClient}>
      <FuelProvider
        fuelConfig={{
          connectors: defaultConnectors(),
        }}
      >
        <App />
      </FuelProvider>
    </QueryClientProvider>
  </React.StrictMode>
);

// If you want to start measuring performance in your app, pass a function
// to log results (for example: reportWebVitals(console.log))
// or send to an analytics endpoint. Learn more: https://bit.ly/CRA-vitals
reportWebVitals();
```

💢 Step 12 (Editing Dapp)

```
nano src/App.tsx
```
💢 Step 13 (Delete everything and Paste this code)

#Replace Contract ID with your own Contract ID

```
import { useEffect, useState } from "react";
import {
  useBalance,
  useConnectUI,
  useIsConnected,
  useWallet
} from '@fuels/react';
import { CounterContractAbi__factory  } from "./sway-api"
import type { CounterContractAbi } from "./sway-api";
 
// REPLACE WITH YOUR CONTRACT ID
const CONTRACT_ID =
  "0x6a99c924a15e3f9c35a759e4534f04560263b2cf512ad7998ab5194c487c3a4c";
 
export default function Home() {
  const [contract, setContract] = useState<CounterContractAbi>();
  const [counter, setCounter] = useState<number>();
  const { connect, isConnecting } = useConnectUI();
  const { isConnected } = useIsConnected();
  const { wallet } = useWallet();
  const { balance } = useBalance({
    address: wallet?.address.toAddress(),
    assetId: wallet?.provider.getBaseAssetId(),
  });
 
  useEffect(() => {
    async function getInitialCount(){
      if(isConnected && wallet){
        const counterContract = CounterContractAbi__factory.connect(CONTRACT_ID, wallet);
        await getCount(counterContract);
        setContract(counterContract);
      }
    }
    
    getInitialCount();
  }, [isConnected, wallet]);
 
  const getCount = async (counterContract: CounterContractAbi) => {
    try{
      const { value } = await counterContract.functions
      .count()
      .get();
      setCounter(value.toNumber());
    } catch(error) {
      console.error(error);
    }
  }
 
  const onIncrementPressed = async () => {
    if (!contract) {
      return alert("Contract not loaded");
    }
    try {
      await contract.functions
      .increment()
      .call();
      await getCount(contract);
    } catch(error) {
      console.error(error);
    }
  };
 
  return (
    <div style={styles.root}>
      <div style={styles.container}>
        {isConnected ? (
          <>
            <h3 style={styles.label}>Counter</h3>
            <div style={styles.counter}>
              {counter ?? 0}
            </div>
 
            {balance && balance.toNumber() === 0 ? (
              <p>Get testnet funds from the <a target="_blank" rel="noopener noreferrer"  href={`https://faucet-testnet.fuel.network/?address=${wallet?.address.toAddress()}`}>Fuel Faucet</a> to increment the counter.</p>
          ) : 
          (
            <button
            onClick={onIncrementPressed}
            style={styles.button}
            >
              Increment Counter
            </button>
          )
          }
            
          <p>Your Fuel Wallet address is:</p>
          <p>{wallet?.address.toAddress()}</p>
          </>
        ) : (
          <button
          onClick={() => {
            connect();
          }}
          style={styles.button}
          >
            {isConnecting ? 'Connecting' : 'Connect'}
          </button>
        )}
      </div>
    </div>
  );
}
 
const styles = {
  root: {
    display: 'grid',
    placeItems: 'center',
    height: '100vh',
    width: '100vw',
    backgroundColor: "black",
  } as React.CSSProperties,
  container: {
    color: "#ffffffec",
    display: "flex",
    flexDirection: "column",
    alignItems: "center",
  } as React.CSSProperties,
  label: {
    fontSize: "28px",
  },
  counter: {
    color: "#a0a0a0",
    fontSize: "48px",
  },
  button: {
    borderRadius: "8px",
    margin: "24px 0px",
    backgroundColor: "#707070",
    fontSize: "16px",
    color: "#ffffffec",
    border: "none",
    outline: "none",
    height: "60px",
    padding: "0 1rem",
    cursor: "pointer"
  },
}
```
💢 Step 14 (Starting Dapp)


```
npm audit fix --force
```
```
npm start
```

💢 Step 15

#You Have to see this in your terminal

#Compiled successfully!

#You can now view frontend in the browser.

Local:            http://localhost:3000
On Your Network:  http://192.168.4.48:3000
  
#If you dont see you Dapp open ports

💢 Step 16 (Opening ports)

```
ufw allow 3000/tcp
```
```
ufw allow 4000/tcp
```

💢 Step 17

#Add this rpc to your wallet networks

```
http://your_ip:4000/graphql
```

