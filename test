package main

import (
	"context"
	"fmt"
	"log"
	"math/big"

	"github.com/ethereum/go-ethereum"
	"github.com/ethereum/go-ethereum/accounts/abi/bind"
	"github.com/ethereum/go-ethereum/common"
	"github.com/ethereum/go-ethereum/ethclient"
)

func main() {
	// Replace these with your actual Ethereum node URL and private keys
	nodeURL := "http://localhost:8545"
	privateKey := "YOUR_PRIVATE_KEY"

	client, err := ethclient.Dial(nodeURL)
	if err != nil {
		log.Fatalf("Failed to connect to the Ethereum client: %v", err)
	}

	// Replace these with the deployed contract addresses
	tokenAAddress := common.HexToAddress("YOUR_TOKEN_A_ADDRESS")
	tokenBAddress := common.HexToAddress("YOUR_TOKEN_B_ADDRESS")
	dexAddress := common.HexToAddress("YOUR_DEX_ADDRESS")

	// Load the contract instances
	tokenA, err := NewTokenA(tokenAAddress, client)
	if err != nil {
		log.Fatalf("Failed to create TokenA instance: %v", err)
	}

	tokenB, err := NewTokenB(tokenBAddress, client)
	if err != nil {
		log.Fatalf("Failed to create TokenB instance: %v", err)
	}

	dex, err := NewDex(dexAddress, client)
	if err != nil {
		log.Fatalf("Failed to create DEX instance: %v", err)
	}

	// Example: Approve the DEX to spend tokens on behalf of the user
	auth, err := bind.NewTransactor(strings.NewReader(privateKey), "YOUR_PRIVATE_KEY_PASSWORD")
	if err != nil {
		log.Fatalf("Failed to create transactor: %v", err)
	}

	amount := big.NewInt(100) // Replace this with the amount you want to approve
	tx, err := tokenA.Approve(auth, dexAddress, amount)
	if err != nil {
		log.Fatalf("Failed to approve tokens: %v", err)
	}

	// Wait for the transaction to be mined
	_, err = bind.WaitMined(context.Background(), client, tx)
	if err != nil {
		log.Fatalf("Failed to wait for transaction mining: %v", err)
	}

	// Example: Add a new token pair to the DEX
	tx, err = dex.AddTokenPair(auth, tokenAAddress, tokenBAddress)
	if err != nil {
		log.Fatalf("Failed to add token pair to DEX: %v", err)
	}

	// Wait for the transaction to be mined
	_, err = bind.WaitMined(context.Background(), client, tx)
	if err != nil {
		log.Fatalf("Failed to wait for transaction mining: %v", err)
	}

	// Example: Swap tokens on the DEX
	amountToSwap := big.NewInt(50) // Replace this with the amount you want to swap
	tx, err = dex.SwapTokens(auth, tokenAAddress, tokenBAddress, amountToSwap)
	if err != nil {
		log.Fatalf("Failed to swap tokens on DEX: %v", err)
	}

	// Wait for the transaction to be mined
	_, err = bind.WaitMined(context.Background(), client, tx)
	if err != nil {
		log.Fatalf("Failed to wait for transaction mining: %v", err)
	}

	// Example: Check updated token balances after the swap
	tokenABalance, err := tokenA.BalanceOf(nil, auth.From)
	if err != nil {
		log.Fatalf("Failed to get TokenA balance: %v", err)
	}

	tokenBBalance, err := tokenB.BalanceOf(nil, auth.From)
	if err != nil {
		log.Fatalf("Failed to get TokenB balance: %v", err)
	}

	fmt.Printf("TokenA Balance: %s\n", tokenABalance.String())
	fmt.Printf("TokenB Balance: %s\n", tokenBBalance.String())
}
