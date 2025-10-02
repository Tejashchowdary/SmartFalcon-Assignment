Hyperledger Fabric 

A blockchain-based account management system using Hyperledger Fabric with Go chaincode and a REST API for account CRUD operations and transaction history.

🛠️ Technologies Used

Hyperledger Fabric v2.5 – Permissioned blockchain framework

Go 1.20+ – Chaincode and REST API development

Docker – Container orchestration

Fabric Gateway – Client SDK for blockchain interaction

WSL2 / Ubuntu – Development environment

🚀 Quick Start
1. Start Network
cd ~/fabric-samples/test-network

# Stop any running network
./network.sh down

# Start network with CA and channel
./network.sh up createChannel -c mychannel -ca

2. Deploy Chaincode
cd ~/fabric-samples/asset-transfer-mywork/chaincode-go
go mod tidy

cd ~/fabric-samples/test-network
./network.sh deployCC -ccn accountcc -ccp ../asset-transfer-mywork/chaincode-go -ccl go

3. Run REST API
cd ~/fabric-samples/asset-transfer-mywork/go-rest-api
go mod tidy

# Build REST API Docker image
docker build -t asset-rest:latest .

# Run REST API container
docker run --rm -p 8080:8080 \
  --network fabric_test \
  -v $(pwd)/../../test-network/organizations:/app/organizations:ro \
  asset-rest:latest

📡 API Usage & Sample Outputs

Base URL: http://localhost:8080/accounts

➕ Create Account

Request:

curl -X POST http://localhost:8080/accounts \
-H "Content-Type: application/json" \
-d '{
  "key":"acc002",
  "DEALERID":"D002",
  "MSISDN":"8888888888",
  "MPIN":"5678",
  "BALANCE":"5000",
  "STATUS":"ACTIVE",
  "TRANSAMOUNT":"0",
  "TRANSTYPE":"INIT",
  "REMARKS":"Initial asset"
}'


Response:

{"result":"created","key":"acc002"}

🔍 Read Account

Request:

curl http://localhost:8080/accounts/acc002


Response:

{
  "DEALERID":"D002",
  "MSISDN":"8888888888",
  "MPIN":"5678",
  "BALANCE":5000,
  "STATUS":"ACTIVE",
  "TRANSAMOUNT":0,
  "TRANSTYPE":"INIT",
  "REMARKS":"Initial asset"
}

✏️ Update Account

Request:

curl -X PUT http://localhost:8080/accounts/acc002 \
-H "Content-Type: application/json" \
-d '{"BALANCE":"2000","REMARKS":"Updated balance"}'


Response:

{"result":"updated","key":"acc002"}

📜 Get History

Request:

curl http://localhost:8080/accounts/acc002/history


Response:

[
  {
    "IsDelete": false,
    "Timestamp": {"seconds":1759238792,"nanos":609019627},
    "TxId":"a94b17151801f1b2ea0976c421594ddc453c85bbf7697de330407d74ece9ccea",
    "Value": {
      "BALANCE":2000,
      "DEALERID":"D002",
      "MPIN":"5678",
      "MSISDN":"8888888888",
      "REMARKS":"Updated balance",
      "STATUS":"ACTIVE",
      "TRANSAMOUNT":0,
      "TRANSTYPE":"INIT"
    }
  },
  {
    "IsDelete": false,
    "Timestamp": {"seconds":1759238818,"nanos":673697488},
    "TxId":"cc678b5d59cd879196f914f0721afd4cc51cb6ff3e23f1a2c3e06149c9ea3ad4",
    "Value": {
      "BALANCE":5000,
      "DEALERID":"D002",
      "MPIN":"5678",
      "MSISDN":"8888888888",
      "REMARKS":"Initial asset",
      "STATUS":"ACTIVE",
      "TRANSAMOUNT":0,
      "TRANSTYPE":"INIT"
    }
  }
]

🧹 Stop Everything
Stop REST API

If running in terminal: Ctrl+C
If running via Docker:

docker ps
docker stop <container_id>

Stop Network
cd ~/fabric-samples/test-network
./network.sh down
