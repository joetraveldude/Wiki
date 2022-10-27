[[_TOC_]]

# Application Information for Developers & Operations

## Customers/Environments
|Customer|Dev Env|Prod Env|Org|Sub|RG 3/Apps|RG 2/Core|RG 1/Network|
|--|--|--|--|--|--|--|--|
|Wallaby SI|Fir|Cedar|NA|TBD|TBD|TBD|TBD|
|Hamster|Fairfax (FF)|RX|Delta|SMT-ME_MP_AGES_PHC_MISSION_DEV2_JALLEN|dev-mp-rg_3-avs|dev-mp-rg_2-avs|dev-mp-rg_1-avs|
|Wallaby SI|Fairfax (FF)|EX|Wallaby|SMT-ME_MP_AGES_PHC_MISSION_DEV2_JALLEN|dev-mp-rg-enclave-resources-wlby-iaas|dev-mp-rg-core-wlby-iaas|dev-mp-rg-network-wlby-iaas|
|FF MVP (demo)|Fairfax (FF)|NA|Bravo|SMT-ME_MP_AGES_PHC_MISSION_DEV3_JALLEN|bm-rg-apps|asciimi-rg_2-apps|asciimi-rg_1-apps|
|Mission2|Fir|Cedar||<on permanent hold> | |

RG 1 ("Network"): Contains the vnet for the enclave
RG 2 ("Core"): Contains the key vault for the enclave
RG 3 ("Apps"): Contains the enclave storage account
Enclaves in Production will have separate subscriptions for each enclave. In FF, we share a sub (due to a deployment limit) which means multiple enclaves exist inside one subscription.

## Customers
|Customer|Dev Env|VNET|KV|SA|
|--|--|--|--|--|
|Hamster|FF|dev-mp-avs-vnet|dev-mp-dndkvbn54t-avs|abssa7yc4p000|
|Wallaby SI|FF|dev-mp-wlby-iaas-vnet|devmpdndkv3qxmuwlbyiaas|enclavesasat3p5a000|
|FF MVP (demo)|FF|asciimi-apps-vnet|asciimi-dndkvpxz6f-apps|bravostagingsa|
The KV and SA are firewalled to only allow access from the VMs on the enclave vnet. The SA should be accessed via the private endpoint for the service (blob, fileshare, table).

## vPAWs/evPAWs
The privileged VM that can run bicep in prod. Login to one of the VMs to get to an evPAW desktop.
|Customer|Dev Env|RG|
|--|--|--|
|Delta|FF|dev-mp-rg-evpaw-avs|
|Wallaby|FF|dev-mp-rg-evpaw-wlby-iaas|

## Application List

|App| Location | Access Method | [App Category](/Above-Baseline-Software/Packaging-Process/Diagrams-for-Apps)| Customer | Deployment POC | Need By Date | License Count | User Count | SQL Needed |
|--|--|--|--|--|--|--|--|--|--|
| Cameo | Cedar | RemoteApp & WebApp| Cat2 | Wallaby SI |  |
| DOORS | Cedar | WebApp | Cat3a | Wallaby SI |  ||75||
| MatLab | Cedar | RemoteApp | Cat1 | Wallaby SI |  ||5||
| Jira | Cedar | WebApp  | Cat3a | Wallaby SI |  ||500||SQL|
| Confluence | Cedar | WebApp | Cat3a | Wallaby SI |  ||500||
| XMLspy | Cedar | RemoteApp | Cat1 | Wallaby SI |  ||1|||
| Cobra | Cedar | RemoteApp | Cat2 | Wallaby SI |  ||||SQL|
| wInsight | Cedar | RemoteApp | Cat2 | Wallaby SI |  ||11||
| ModelCenter | Cedar | RemoteApp | Cat1 | Wallaby SI |  ||4||
| STK Premium (Space) with Scheduler | Cedar | RemoteApp | Cat1 | Wallaby SI || |1|||
| Jira | RX | WebApp | Cat3a | Hamster | ||Lic from Cedar||SQL |
| DokuWiki | RX | WebApp | Cat3a | Hamster |||Lic from Cedar||
| Jira | EX | WebApp | Cat3a | Rabbit|||25||
| Confluence | EX | WebApp | Cat3a | Rabbit|||25||
| Full Monte & Schedule Inspector | EX | RemoteApp | Cat2 | Wallaby SI|||1||
| XMLspy | EX | RemoteApp | Cat1 | Wallaby SI | ||10|||
| STK Premium (Space) with Scheduler | EX | RemoteApp | Cat1 | Wallaby SI || |1|||
| Beyond Compare | RX | TBD | TBD | Hamster || |250|||

Some apps may be deployed in multiple ways (Cat1 by itself or Cat2 connected to other engineering tools)


