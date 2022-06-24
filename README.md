# Lightning evouchers

Non-custodial evouchers for simpler lightning payments

# How it works

The sender picks an amount for their voucher, creates a preimage, and hashes it. They provide the hash to Lightning Escrow or another lightning service provider (LSP) and ask them for an invoice with that payment hash and for the amount they picked. The LSP provides them with such an invoice, which the sender tries to pay with their wallet. The LSP cannot settle the invoice at this stage because they do not have the preimage (the sender does), but, because the money is locked into an htlc which the LSP has the raw text of, the sender can go offline at this stage if they want.

At their convenience, the sender sends the preimage and the amount to a recipient. This can be done via email, twitter dms, or any other communication method. The recipient then creates an invoice for the same amount with the same payment hash. They show this to the LSP, who verifies that the amount matches the voucher amount, the payment hash matches the one in the voucher, and the new invoice expires before the voucher payment expires. If all of that checks out, they pay the recipient's invoice, thus giving the voucher to the recipient. To settle this payment, the recipient must give the LSP the preimage. Once the LSP has the preimage, they settle the original payment that the sender made to the LSP, thus reimbursing themselves and completing the protocol.

# Why it matters

A significant drawback of the lightning network, compared to using the regular bitcoin network, is lightning's interactivity. The sender and the recipient have to talk to one another in a synchronous manner to generate an invoice and then pay it. This presents a significant user interface challenge which is especially poignant when onboarding new users: anyone used to a system like venmo or paypal finds lightning initially confusing, because in traditional electronic payment systems, the sender can give you any amount of money at any time without consulting you beforehand except to get your email or some other way to contact you, but with the lightning network, this is reversed, the recipient has to take action first by creating an invoice.

Lightning evouchers reverse that flow by making the sender act first: the sender creates an evoucher which they can then send to the recipient using any contact method. The recipient doesn't need anything but a lightning wallet, and, since the vouchers last for several days before they expire, the recipient doesn't even need to get a lightning wallet immediately -- they've got a bit of time to wait.

# Is this custodial?

No. This system uses hodl invoices to delay settlement of the evoucher until the recipient gets online to claim it. Only someone who knows the preimage can take the money from the evoucher, and the preimage is only known to the sender and the recipient. The LSP has no power to steal funds, the worst thing they can do is refuse to settle the evoucher, which will cause it to time out (eventually) and return to the sender's wallet.

# Is this like lnurl?

Sort of. One of the things you can do with lnurl is create an lnurl withdraw code, which, like a lightning evoucher, lets the recipient take money from the withdraw code at their leisure. But withdraw codes have some downsides, including these: the sender has to run a web server, which is daunting for most people. As a result, most of the people who use withdraw codes first deposit money into a centralized custodian's service, then they create the withdraw code from there. Also, with a withdraw code, the sender has power to "take back" the money at any time prior to the point when the intended recipient uses the withdraw code. Lightning evouchers improve on both of these: the sender does not have to run a web server, they can fund the voucher and then go offline if they want. And they also can't take back the money at any time they want -- the recipient has several days to withdraw the money before the voucher expires and the money returns to the sender's wallet. So it is like lnurl, but has some advantages in a withdrawal context.

# Are lightning evouchers an attack on the lightning network?

Yes. They exploit a vulnerability in the lightning network that is not yet patched. (Don't worry, when the vulnerability is patched, evouchers will still work just fine, in fact they'll probably work better.) The vulnerability involves special types of invoices called hodl invoices, which lock up funds on routing nodes for longer time periods than routing nodes typically expect. Every lightning voucher locks up the funds of routing nodes for up to 1008 bitcoin blocks (about a week), and during that time those routing nodes cannot make any money off of those locked up funds. This makes them pretty angry, and it's totally fixable.

So far there are not very many services that use hodl invoices so fixing the vulnerability has been a low priority for lightning developers, but one promising way to fix it is by adjusting the lightning routing software so that routing nodes charge higher fees for invoices that have longer locktimes. That would recompense routing node operators for the time their money gets locked up and would still allow lightning evouchers to work just fine. (The fees to send money via a lightning evoucher would just get a little bit higher.)
