---
title: "Commands"
permalink: /pond/features-commands
toc: true
---
Hi, this page will tell you about the commands!

{% raw %}
<div class="mermaid">
stateDiagram-v2
    direction LR
    [*] --> IDLE
    IDLE --> EXECUTING:[IsTriggered = True]
    EXECUTING --> EXECUTING
    EXECUTING --> IDLE: [IsCompleted = True]
    EXECUTING --> [*]: [IsCompleted = True <br> and RunOnce]
</div>
{% endraw %}
