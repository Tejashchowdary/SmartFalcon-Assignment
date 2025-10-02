Account Management System - Hyperledger Fabric
A blockchain-based account management system using Hyperledger Fabric with Go chaincode and REST API.
Technologies Used

Hyperledger Fabric v2.5 - Permissioned blockchain framework
Go 1.20+ - Chaincode and REST API development
Docker - Container orchestration
Fabric Gateway - Client SDK for blockchain interaction
WSL2/Ubuntu - Development environment

Quick Start
1. Start Network
bashcd ~/fabric-samples/test-network
./network.sh down
./network.sh up createChannel -c mychannel -ca
2. Deploy Chaincode
bashcd ~/fabric-samples/asset-transfer-mywork/chaincode-go
go mod tidy

cd ~/fabric-samples/test-network
./network.sh deployCC -ccn accountcc -ccp ../asset-transfer-mywork/chaincode-go -ccl go
3. Run REST API
bashcd ~/fabric-samples/asset-transfer-mywork/go-rest-api
go mod tidy
docker build -t asset-rest:latest .

docker run --rm -p 8080:8080 \
  --network fabric_test \
  -v $(pwd)/../../test-network/organizations:/app/organizations:ro \
  asset-rest:latest
API Usage
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
  -d '{"BALANCE":"15000","REMARKS":"Updated"}'
Get History
bashcurl http://localhost:8080/accounts/acc001/history
Stop Everything
bash# Stop REST API: Press Ctrl+C

# Stop network
cd ~/fabric-samples/test-network
./network.sh down
Troubleshooting
Network won't start:
bash./network.sh down
docker system prune -f
./network.sh up createChannel -c mychannel -ca
Permission errors:
bashsudo chown -R $(id -u):$(id -g) ~/fabric-samples
Missing go.sum:
bashcd ~/fabric-samples/asset-transfer-mywork/chaincode-go
go mod tidy
