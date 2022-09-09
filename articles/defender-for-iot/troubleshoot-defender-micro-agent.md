---
title: Defender IoT micro agent troubleshooting (Preview)
description: Learn how to handle unexpected or unexplained errors.
ms.date: 4/5/2021
ms.topic: reference
---

# Defender IoT micro agent troubleshooting (Preview)

If an unexpected error occurs, you can use these troubleshooting methods in an attempt to resolve the issue. You can also reach out to the Azure Defender for IoT product team for assistance as needed.   

## Service status 

To view the status of the service: 

1. Run the following command

    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```

1. Check that the service is stable by making sure it is `active`, and that the uptime in the process is appropriate.

    :::image type="content" source="media/troubleshooting/active-running.png" alt-text="Ensure your service is stable by checking to see that it is active and the uptime is appropriate.":::

If the service is listed as `inactive`, use the following command to start the service:

```azurecli
systemctl start defender-iot-micro-agent.service 
```

You will know that the service is crashing if, the process uptime is less than 2 minutes. To resolve this issue, you must [review the logs](#review-the-logs).

## Validate micro agent root privileges

Use the following command to verify that the Defender IoT micro agent service is running with root privileges.

```azurecli
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="Verify the Defender for IoT micro agent service is running with root privileges.":::
## Review the logs 

To review the logs, use the following command:  

```azurecli
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

### Quick log review

If an issue occurs when the micro agent is run, you can run the micro agent in a temporary state, which will allow you to view the logs using the following command:

```azurecli
sudo systectl stop defender-iot-micro-agent
cd /var/defender_iot_micro_agent/
sudo ./defender_iot_micro_agent
```

## Restart the service

To restart the service, use the following command: 

```azurecli
sudo systemctl restart defender-iot-micro-agent  
```

## Next steps

Check out the [Feature support and retirement](edge-security-module-deprecation.md).
