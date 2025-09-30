Hyperledger Fabric Account Management System
A complete blockchain-based account management system built with Hyperledger Fabric, featuring a Go chaincode smart contract and REST API.
Project Structure
fabric-samples/            
└── asset-transfer-mywork/
    ├── chaincode-go/          # Smart contract
    └── go-rest-api/           # REST API server
Prerequisites

Docker & Docker Compose
Go 1.20+
Hyperledger Fabric binaries (via bootstrap script)
curl (for testing)

Quick Start
1. Start the Network
bashcd ~/fabric-samples/test-network
./network.sh down
./network.sh up createChannel -c mychannel -ca
2. Deploy Chaincode
bashcd ~/fabric-samples/asset-transfer-mywork/chaincode-go
go mod tidy

cd ~/fabric-samples/test-network
./network.sh deployCC -ccn accountcc -ccp ../asset-transfer-mywork/chaincode-go -ccl go
3. Build REST API
bashcd ~/fabric-samples/asset-transfer-mywork/go-rest-api
go mod tidy
docker build -t asset-rest:latest .
4. Run REST API
bashdocker run --rm -p 8080:8080 \
  --network fabric_test \
  -v $(pwd)/../../test-network/organizations:/app/organizations:ro \
  asset-rest:latest
API Endpoints
Create Account
bashcurl -X POST http://localhost:8080/accounts \
  -H "Content-Type: application/json" \
  -d '{
    "key":"acc001",
    "DEALERID":"D001",
    "MSISDN":"9876543210",
    "MPIN":"1234",
    "BALANCE":"10000",
    "STATUS":"ACTIVE",
    "TRANSAMOUNT":"0",
    "TRANSTYPE":"INIT",
    "REMARKS":"Initial account"
  }'
Read Account
bashcurl http://localhost:8080/accounts/acc001
Update Account
bashcurl -X PUT http://localhost:8080/accounts/acc001 \
  -H "Content-Type: application/json" \
  -d '{
    "BALANCE":"15000",
    "REMARKS":"Balance updated"
  }'
Get Account History
bashcurl http://localhost:8080/accounts/acc001/history
Account Fields

DEALERID: Dealer identifier
MSISDN: Mobile number
MPIN: PIN code
BALANCE: Account balance
STATUS: Account status (ACTIVE/INACTIVE)
TRANSAMOUNT: Transaction amount
TRANSTYPE: Transaction type
REMARKS: Additional notes

# Stop network
cd ~/fabric-samples/test-network
./network.sh down
Network Details

Channel: mychannel
Chaincode: accountcc
Organizations: Org1, Org2
REST API Port: 8080
Docker Network: fabric_test

Notes

The REST API connects to the Fabric network using Org1 Admin credentials
All certificates are mounted read-only from the test-network
Transactions are submitted to both Org1 and Org2 peers
Account history provides complete transaction audit trail