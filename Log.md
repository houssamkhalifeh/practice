```mermaid

flowchart TD
    A[Application] -->|Send log message| B[LoggingManager]

    subgraph LoggingManager
        B1["QueueUIMessage(SEVMessage msg)"]
        B2[ConcurrentQueue<SEVMessage> _messageQueue]
        B3[NewMessage Event]
        B4[Serilog Log.Logger]
    end

    B --> B1
    B1 --> B2
    B1 --> B3
    B1 --> B4

    B2 -->|Maintains last 10 messages| B2
    B3 -->|Notifies UI| C[UI Components]
    B4 -->|Writes log to file| D[logs\Log.log]

    classDef queue fill:#f9f,stroke:#333,stroke-width:1px;
    class B2 queue;
    classDef log fill:#bbf,stroke:#333,stroke-width:1px;
    class B4 log;
```
```mermaid

classDiagram
    class LoggingManager {
        <<static>>
        - ConcurrentQueue~SEVMessage~ _messageQueue
        - int MaxQueueSize = 10
        + event EventHandler~SEVMessage~ NewMessage
        + IReadOnlyCollection~SEVMessage~ MessageQueue
        + static void ErrorMessage(string msg)
        + static void WarningMessage(string msg)
        + static void InformationMessage(string msg)
        + static void ErrorMessage(string format, params object[] objs)
        + static void WarningMessage(string format, params object[] objs)
        + static void InformationMessage(string format, params object[] objs)
        + static void ExceptionMessage(Exception ex)
        + static void Error(string msg)
        + static void Warning(string msg)
        + static void Information(string msg)
        + static void Error(string format, params object[] objs)
        + static void Warning(string format, params object[] objs)
        + static void Information(string format, params object[] objs)
        + static void Exception(Exception ex)
        - static void QueueUIMessage(SEVMessage msg)
        - static void LogAndQueue(SEVMessage msg, Action~string~ logAction)
    }

    class SEVMessage {
        + string Text
        + static SEVMessage Error(string msg)
        + static SEVMessage Warning(string msg)
        + static SEVMessage Information(string msg)
        + static SEVMessage Error(string format, params object[] objs)
        + static SEVMessage Warning(string format, params object[] objs)
        + static SEVMessage Information(string format, params object[] objs)
    }

    LoggingManager "1" --> "*" SEVMessage : uses
