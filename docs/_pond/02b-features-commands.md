---
title: "Commands"
permalink: /pond/features-commands
toc: true
---
This is how the commands are executed:

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

###Available Commands' Usage
