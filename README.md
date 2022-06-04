# Stable Coin Remittance System

## Design Rationale

### Main Client Workflow

#### Deposit Sequence

```mermaid
sequenceDiagram
    Client->>Administrator: Deposit Request (via Bot)
    Administrator->>Client: Deposit Request Confirmation: AgentA (via Bot)
    Client-->>AgentA: Cash Deposit
    Client->>Administrator: Cash Deposit Claim: Address and Amount (via Bot)
    Administrator->>AgentA: Cash Deposit Confirmation Request (via Bot)
    AgentA->>Administrator: Deposit Confirmation (via Bot)
    Administrator-->>Client: Tokens Minted (automatically)
```

#### Withdrawal Sequence

```mermaid
sequenceDiagram
    Client->>Administrator: Withdrawal Request (via Bot)
    Administrator->>Client: Withdrawal Request Confirmation: AgentB (via Bot)
    Client-->>Escrow: Token Escrow (via Bot)
    AgentB-->>Client: Cash Withdrawal
    AgentB->>Administrator: Cash Withdrawal Confirmation (via Bot)
    Administrator->>Client: Cash Acceptance Request (via Bot)
    Client->>Administrator: Cash Acceptance Confirmation (via Bot)
    Escrow-->>Escrow: Tokens Burned (automatically)
```

### Telegram Bot Features

#### Registration

Telegram bots by design can be found and started by anyone, however the bot can stay inactive until it can confirm with Telegram user either invite code or some sort of passphrase. 

#### Telegram Bot Activation Sequence

```mermaid
sequenceDiagram
    Client-->>Bot: Connect
    Bot->>Client: Invitation Code Request
    Client->>Bot: Invitation Code Submit
    Bot->>Client: Shows Main Menu
```

#### Bot Main Menu

Main Menu is only shown for activated user (the one who presented invitation code).

```mermaid
flowchart TD
    mm[Main Menu] --> cw
    mm --> cd
    cw[Cash Withdrawal] --> ncw
    cd[Cash Deposit] --> ncd
    cd -- Only for Client --> ccd[Claim Cash Deposit]
    cw -- Only for Agent --> ccw[Claim Cash Withdrawal]
    ncd[New Cash Deposit] --> ac
    ncw[New Cash Withdrawal] --> ac
    ac[Amount Confirmation] --> ls
    ls[Location Selection] --> Chat?
```

#### Bot Admin Menu

```mermaid
flowchart TD
    mm[Main Menu] --> ic
    ic[Invitation Codes] --> uic[Create User Invitation]
    ic --> aic[Create Agent Invitation Code]
```

## Stable Coin (Token) Requirements

Stable coin is a TRC20-compliant token based on TRC20 Mintable and Burnable Contract with Role-based Access Control System.

### Implemented methods and features

#### Basic features

The contract should provide methods and functions to retrieve Token parameters for anyone (aka Public methods):

- Name `trc20.name`
- Symbol `trc20.symbol`
- Decimal places `trc20.decimals`
- Total Supply `trc20.totalSupply`
- Balance for specified address `trc20.balanceOf`

#### Token owner features

The contract should provide methods and function to perform _transfer_ and _approve_ operations with Tokens:

- Transfer `trc20.transfer`
- Allowance (show remaining approved tokens) `trc20.allowance`
- Approve (allowance for third party to transfer specified amount of tokens) `trc20.approve`
- Transfer From Third Party Address (transfer Tokens within remaining pre-approved allowance) `trc20.transferFrom`
- Increase Allowance `trc20.increaseAllowance`
- Decrease Allowance `trc20.decreaseAllowance`

#### Token management features

The contract should allow designated managers to mint, burn, add minters, remove minters, pause or unpause contract:

- Mint `trc20.mint`
- Burn `trc20.burn`
- Burn From Address `trc20.burnFrom`
- Pause `trc20.pause`
- Unpause `trc20.unpause`
- Add Minter `trc20.addMinter`
- Renounce Minter `trc20.renounceMinter`
