# Update NCN BIOS TPM State

## Description

Enable TPM (Trusted Platform Module) in the BIOS on the new NCN, if TPM is being used on the other NCNs.
**Skip this step if TPM is not being used** and proceed to the next step to [Boot NCN and Configure](Boot_NCN.md).
It is not required to disable TPM when it is not being used. Many types of hardware come with TPM enabled by default.

## Current capabilities

The following table lists the type of hardware where the TPM State can be enabled. Not all models from a given manufacturer will support TPM.

| **Manufacturer** | **Type**     |
| ---------------- | ------------ |
| Gigabyte         | `nodeBMC`    |
| HPE              | `nodeBMC`    |

## Procedure

1. (`ncn-mw#`) Select the xname of the BMC Node

    ```bash
    export XNAME_BMC=x3000c0s4b0n0
    ```

1. (`ncn-mw#`) Check TPM State

    ```bash
    cray scsd bmc bios describe tpmstate $XNAME_BMC --format json
    ```

    Example output for the  Disabled Case:

    ```text
    {
        "Current" : "Disabled"
        "Future" : "Disabled"
    }
    ```

    If the response is `NotPresent` then setting TPM is not supported.
    The remaining steps should be skipped. Proceed to the next step to [Boot NCN and Configure](Boot_NCN.md).

    ```text
    {
        "Current" : "NotPresent"
        "Future" : "NotPresent"
    }
    ```

    If the response is an error, then setting TPM is not supported by SCSD or is not supported by the hardware.
    This includes any usage errors from the `cray` command.
    The remaining steps should be skipped. Proceed to the next step to [Boot NCN and Configure](Boot_NCN.md).

1. (`ncn-mw#`) Enable TPM State if it is Disabled

    If the the previous step showed that TPM was Disabled then Enable it with the following request.

    ```bash
    cray scsd bmc bios update tpmstate $XNAME_BMC --future Enabled
    ```

1. (`ncn-mw#`) Check that the Future value is set.

    ```bash
    cray scsd bmc bios describe tpmstate $XNAME_BMC --format json
    ```

    Example output:

    ```text
    {
        "Current" : "Disabled"
        "Future" : "Enabled"
    }
    ```

    The new TPM state will take effect the next time the node is booted.

1. Repeat these steps for other nodes

    If the hardware has multiple nodes, then repeat these steps for the second node. For example, a server might have these nodes: `x3000c0s4b0n0` and `x3000c0s4b0n1`

## Next Step

Proceed to the next step to [Boot NCN and Configure](Boot_NCN.md) or return to the main [Add, Remove, Replace, or Move NCNs](../Add_Remove_Replace_NCNs.md) page.
