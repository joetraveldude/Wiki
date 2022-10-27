# Goals for Gates Edge

## Product Goal

Gates Edge enables air-gapped, DDIL, and hybrid customers to use a multi-tenant Microsoft cloud, virtual desktop, and office services in an on-prem form-factor when they don’t have access to the Microsoft hyperscale resources. Gates Edge is delivered as a security-enhanced Azure cloud edge accelerator that satisfies the strongest compliance requirements, implements Zero Trust platform.

## Sprint Goals

### Goal for Sprint 36: 16-MAY-22 - 03-JUN-22

Sidecar Team:

- Automate (install/configure) IaC/CaC and document the following components: WSUS and YUM.
- Document steps needed for installation of ACAS Scanner (Nessus Tenable).
- Document any manual deployment steps, location of scripts, and complete regression testing for CA.
- Introduce 3rd node to Hyper-V cluster and document deployment steps. Determine if any automation can be completed.
- Procurement process for Cohesity appliance.

Baseline Team:

- Automate the following components: ADO, ACAS (Nessus Tenable), HBSS (McAfee ePO), Logging (Splunk), WSUS, and YUM.
- Finalize SQL cluster installation


### Goal for Sprint 34: 18-APR-22 - 29-APR-22

Sidecar Team:

- Stand up initial Hyper-V Sidecar environment in hyperscale to include Hyper-V clustering

Core Team:

- Virtual Sidecar connected to Gates Edge network (network based on MLZ Edge)
- Develop strategy for Linux Configuration as Code (CaC)

