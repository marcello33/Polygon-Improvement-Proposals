---
PIP: 87
Title: Fixed Cost Payments Revenue Program
Authors: Sandeep Nailwal, David Silverman, John Egan, Jeremy Brenner
Status: Draft
Type: Core
Date: 2026-04-30
---

## Abstract
Polygon has become the leading payments chain processing over $2.4T in payments volume. With over 55% market share of USDC weekly transfers and hitting an all time high of 45MM USD-based stablecoin transactions in a single week. This proposal calls for the creation of a new fixed price program for payment companies building on Polygon to get consistent pricing in fiat for blockspace, and for those payments to flow to stakers and tokenholders via the PIP-82, PIP-85 and EIP-1559 systems. 

To accomplish this, payment relayers will be whitelisted for rebate under an expansion of the PIP-82 system, with additional rebate being processed at the PIP-85 level to account for priority fees. Fiat revenue from this system will be converted to stablecoins and distributed both via the PIP-85 collector (0x3ef) to Validators, Block Producers and Stakers (POL will be bought back from the market for the staker distribution and POL will be bought back from the market and transferred to the PIP-24 collector (0x7A) for the burn. The ratio of these actions will be in line with the trailing periods ratio of priority fees to base fees accordingly.

These splits, much like the existing PIP-65, PIP-82 and PIP-85 distributions will be audited by Regen Financial and may be changed at the directive of PIPs being approved by Polygon Governance.

## Definitions 
PIP-65 Fee Distribution System: A process by which Polygon Labs and Regen Financial distribute protocol funds according to formulas passed as PIPs by the Polygon Protocol Governance Process. To date it covers the EIP-1559 Priority Fee System for Polygon PoS, the PIP-82 Agentic Rebate System, and the PIP-85 Validator Priority Fee Program.
## Motivation
Polygon has become a leading settlement layer for payments, but its fee model remains variable due to gas price dynamics. This creates challenges for payment companies that operate in fiat and require predictable transaction costs.
For payments companies, volatility in transaction fees introduces operational and pricing complexity - notably lack of predictability in pricing -  limiting the ability of payment processors and fintech platforms to scale onchain. As a result, a significant portion of payment volume remains offchain despite Polygon’s cost and performance advantages.
This proposal introduces a fixed-cost framework to provide consistent, predictable, and fiat-denominated pricing for blockspace. This reduces uncertainty for payment providers and lowers the barrier to onboarding high-volume payment flows.
The design ensures that resulting economic activity accrues to the protocol. Fiat revenue is converted and distributed through existing mechanisms, reinforcing validator, builder, and staker incentives while supporting POL buybacks.
Overall, this proposal aligns Polygon’s pricing model with the requirements of payment providers while strengthening the link between network usage and tokenholder value.

## Specification

Direct the managers of the PIP-65, PIP-82 and PIP-85 program to make the necessary formula changes to support this PIP. 
Direct the Polygon foundation to put out regular updates on the number of tokens bought back, and how many have been distributed to stakers and how many have been transferred to the burner.
## Backward Compatibility
This change is 100% backwards compatible with all existing systems, as it only requires offchain coordination.
## Copyright
All copyrights and related rights in this work are waived under CC0 1.0 Universal.
