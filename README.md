Hyperledger Fabric Account Management System
A blockchain-based account management system built on Hyperledger Fabric, featuring smart contract chaincode and a REST API for managing financial accounts.
Project Structure
asset-transfer-mywork/
├── chaincode-go/           # Smart contract (chaincode)
│   ├── account_chaincode.go
│   ├── go.mod
│   └── go.sum
└── go-rest-api/           # REST API service
    ├── main.go
    ├── Dockerfile
    ├── go.mod
    └── go.sum
Prerequisites

Go 1.23 or higher
Docker and Docker Compose
Hyperledger Fabric 2.5.x
Hyperledger Fabric Samples repository

Setup Instructions
1. Install Hyperledger Fabric
Follow the official Hyperledger Fabric documentation:

Getting Started
Install Prerequisites

2. Clone Fabric Samples
bashcurl -sSL https://bit.ly/2ysbOFE | bash -s
cd fabric-samples
3. Add This Project
Clone this repository into the fabric-samples directory:
bashcd fabric-samples
git clone <your-repo-url> asset-transfer-mywork
4. Start the Test Network
bashcd test-network
./network.sh down
./network.sh up createChannel -c mychannel -ca
5. Deploy the Chaincode
bash./network.sh deployCC -ccn accountcc -ccp ../asset-transfer-mywork/chaincode-go -ccl go
Smart Contract (Chaincode) Functions
Account Structure
gotype Account struct {
    DealerID    string  `json:"DEALERID"`
    MSISDN      string  `json:"MSISDN"`
    MPIN        string  `json:"MPIN"`
    Balance     float64 `json:"BALANCE"`
    Status      string  `json:"STATUS"`
    TransAmount float64 `json:"TRANSAMOUNT"`
    TransType   string  `json:"TRANSTYPE"`
    Remarks     string  `json:"REMARKS"`
}
Available Functions

CreateAccount - Create a new account
ReadAccount - Read account details
UpdateAccount - Update account information
GetAccountHistory - Retrieve transaction history
AccountExists - Check if account exists

REST API
Build Docker Image
bashcd asset-transfer-mywork/go-rest-api
docker build -t asset-rest:latest .
Run the REST API
bashdocker run --rm -p 8080:8080 \
  --network fabric_test \
  -v $(pwd)/../../test-network/organizations:/app/organizations:ro \
  asset-rest:latest
API Endpoints
1. Create Account
bashcurl -X POST http://localhost:8080/accounts \
  -H "Content-Type: application/json" \
  -d '{
    "key": "acc001",
    "DEALERID": "D001",
    "MSISDN": "9876543210",
    "MPIN": "1234",
    "BALANCE": "10000",
    "STATUS": "ACTIVE",
    "TRANSAMOUNT": "0",
    "TRANSTYPE": "INIT",
    "REMARKS": "Initial account"
  }'
Response:
json{"result":"created","key":"acc001"}
2. Read Account
bashcurl http://localhost:8080/accounts/acc001
Response:
json{
  "DEALERID": "D001",
  "MSISDN": "9876543210",
  "MPIN": "1234",
  "BALANCE": 10000,
  "STATUS": "ACTIVE",
  "TRANSAMOUNT": 0,
  "TRANSTYPE": "INIT",
  "REMARKS": "Initial account"
}
3. Update Account
bashcurl -X PUT http://localhost:8080/accounts/acc001 \
  -H "Content-Type: application/json" \
  -d '{
    "BALANCE": "15000",
    "REMARKS": "Balance updated"
  }'
Response:
json{"result":"updated","key":"acc001"}
4. Get Account History
bashcurl http://localhost:8080/accounts/acc001/history
Response:
json[
  {
    "IsDelete": false,
    "Timestamp": {"seconds": 1759397987, "nanos": 869326844},
    "TxId": "5c021a04...",
    "Value": {
      "BALANCE": 15000,
      "DEALERID": "D001",
      "MPIN": "1234",
      "MSISDN": "9876543210",
      "REMARKS": "Balance updated",
      "STATUS": "ACTIVE",
      "TRANSAMOUNT": 0,
      "TRANSTYPE": "INIT"
    }
  }
]
Testing
Test the Chaincode Directly
bash# Set environment variables
export PATH=${PWD}/../bin:$PATH
export FABRIC_CFG_PATH=$PWD/../config/
export CORE_PEER_TLS_ENABLED=true
export CORE_PEER_LOCALMSPID="Org1MSP"
export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
export CORE_PEER_ADDRESS=localhost:7051

# Create an account
peer chaincode invoke -o localhost:7050 \
  --ordererTLSHostnameOverride orderer.example.com \
  --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem \
  -C mychannel -n accountcc \
  --peerAddresses localhost:7051 --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt \
  --peerAddresses localhost:9051 --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt \
  -c '{"function":"CreateAccount","Args":["acc001","D001","9876543210","1234","10000","ACTIVE","0","INIT","Initial account"]}'

# Query an account
peer chaincode query -C mychannel -n accountcc \
  -c '{"function":"ReadAccount","Args":["acc001"]}'
Cleanup
To stop and clean up the network:
bashcd test-network
./network.sh down
Architecture
Components

Chaincode (Smart Contract) - Written in Go, manages account state on the blockchain
REST API - Go-based HTTP server that interacts with the chaincode via Fabric Gateway
Hyperledger Fabric Network - 2 organizations (Org1, Org2), 1 orderer, using Raft consensus

Data Flow
Client (curl/Postman)
    ↓
REST API (Docker Container)
    ↓
Fabric Gateway SDK
    ↓
Peer Nodes (Org1, Org2)
    ↓
Chaincode (Smart Contract)
    ↓
Ledger (World State + Blockchain)
Features

Account creation with validation
Secure account updates
Complete transaction history tracking
RESTful API for easy integration
Immutable audit trail via blockchain
Multi-organization endorsement

Technologies Used

Hyperledger Fabric 2.5.x
Go 1.23
Docker & Docker Compose
Fabric Contract API (Go)
Fabric Gateway SDK (Go)
gRPC for peer communication

Security Considerations

TLS enabled for all communications
Certificate-based authentication
Multi-organization endorsement policy
MPIN stored (consider hashing in production)
Private keys stored securely in MSP

Future Enhancements

Add transaction between accounts
Implement role-based access control
Add balance validation rules
Implement event notifications
Add pagination for history queries
Enhanced error handling and logging

License
This project was created as part of a Hyperledger Fabric internship assignment.
