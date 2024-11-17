# CoffeeShopPOS Contract

This Solidity program is a simple **Point of Sale (POS) system** for a coffee shop. It allows users to **purchase credits** (using Ether) and use those credits to purchase items from a menu. The contract also demonstrates basic error handling with `require()`, `revert()`, and `assert()` functions to ensure correct behavior.

## Description

The **CoffeeShopPOS** contract provides functionality to:
1. **Purchase credits** by sending Ether to the contract.
2. **View available credits**.
3. **Purchase items** from the menu using the credits.
4. **Event logging** for each credit purchase and item purchase.

The contract manages the coffee shop menu with 4 items and their prices in credits, and allows customers to buy items by spending their credits.

### Features:
- **Purchase Credits**: Users can send Ether to purchase credits (1 ETH = 1,000 credits).
- **View Available Credits**: Users can check how many credits they have.
- **Menu System**: The menu includes four different items with associated credit costs:
  - Spanish Latte (120 credits)
  - White Chocolate Mocha (150 credits)
  - Americano (100 credits)
  - Dirty Matcha (160 credits)
- **Purchase Item**: Users can purchase items from the menu by spending their credits.
- **Event Logging**: Emitting events when credits are purchased or items are purchased.

### Error Handling:
- **`require()`**: Ensures that the conditions for purchasing credits or items are met (e.g., positive purchase amounts).
- **`revert()`**: Used for handling insufficient credits when trying to purchase an item.
- **`assert()`**: Ensures that credits are updated correctly after purchasing an item.

## Getting Started

### Executing the Program

To run this program, you can use **Remix**, an online Solidity IDE. Follow these steps:

1. Go to the Remix website: [Remix IDE](https://remix.ethereum.org/).
2. Create a new file by clicking the "+" icon in the left-hand sidebar. Save it with a `.sol` extension (e.g., `CoffeeShopPOS.sol`).
3. Copy and paste the following code into the file:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

contract CoffeeShopPOS {
    struct MenuItem {
        string name;
        uint16 price; // For credits
    }

    mapping(uint => MenuItem) public menu;
    mapping(address => uint) private credits;

    address public owner;

    event CreditsPurchased(address indexed customer, uint amount);
    event ItemPurchased(address indexed customer, string itemName, uint16 price);

    constructor() {
        owner = msg.sender;

        // Initialize the menu
        menu[1] = MenuItem("Spanish Latte", 120);
        menu[2] = MenuItem("White Chocolate Mocha", 150);
        menu[3] = MenuItem("Americano", 100);
        menu[4] = MenuItem("Dirty Matcha", 160);
    }

    function purchaseCredits() public payable {
        require(msg.value > 0, "Send ETH to purchase credits.");
        uint purchasedCredits = msg.value / 1e15; // 1 ETH = 1,000 credits
        credits[msg.sender] += purchasedCredits;

        emit CreditsPurchased(msg.sender, purchasedCredits);
    }

    function getCredits() public view returns (uint) {
        return credits[msg.sender];
    }

    function purchaseItem(uint itemId) public {
        require(menu[itemId].price > 0, "Invalid item.");

        uint16 itemPrice = menu[itemId].price;
        string memory itemName = menu[itemId].name;

        if (credits[msg.sender] < itemPrice) {
            revert("Insufficient credits to purchase this item."); // Handle insufficient credits
        }

        // Deduct credits and ensure the updated value
        uint initialCredits = credits[msg.sender];
        credits[msg.sender] -= itemPrice;
        assert(credits[msg.sender] == initialCredits - itemPrice); // Ensure credits are updated correctly

        // Emit the ItemPurchased event
        emit ItemPurchased(msg.sender, itemName, itemPrice);
    }
}
```

# Authors
Justin Bulot

Email - 202110965@fit.edu.ph

## License

This project is licensed under the MIT License - see the LICENSE.md file for details
