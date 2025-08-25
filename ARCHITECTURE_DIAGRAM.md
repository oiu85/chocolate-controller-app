# 🏗️ ESP32 Temperature Control System - Architecture & Data Flow

## 📊 System Architecture Overview

```
┌─────────────────────────────────────────────────────────────────────────────────────┐
│                                    PRESENTATION LAYER                              │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │                              Wails Frontend                                │   │
│  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐          │   │
│  │  │   Home      │ │   Manual    │ │   Recipe    │ │    Admin    │          │   │
│  │  │   Page      │ │   Control   │ │   Manager   │ │    Page     │          │   │
│  │  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘          │   │
│  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐          │   │
│  │  │   Logger    │ │   Operator  │ │   Splash    │ │   Charts    │          │   │
│  │  │   Page      │ │    Page     │ │   Screen    │ │             │          │   │
│  │  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘          │   │
│  └─────────────────────────────────────────────────────────────────────────────┘   │
│                                    ▲                                              │
│                                    │ Wails Events & Bindings                       │
└────────────────────────────────────┼──────────────────────────────────────────────┘
                                     │
┌────────────────────────────────────┼──────────────────────────────────────────────┐
│                              APPLICATION LAYER                                   │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │                              Main App (app.go)                             │   │
│  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐          │   │
│  │  │   Core      │ │   ESP32     │ │   Recipe    │ │   RabbitMQ  │          │   │
│  │  │   App       │ │   Control   │ │   Manager   │ │   Client    │          │   │
│  │  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘          │   │
│  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐          │   │
│  │  │   Setup     │ │ Monitoring │ │   Utils     │ │   Admin     │          │   │
│  │  │   Manager   │ │   Service   │ │   Helper    │ │   Settings  │          │   │
│  │  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘          │   │
│  └─────────────────────────────────────────────────────────────────────────────┘   │
│                                    │                                              │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │                           Application Services                              │   │
│  │  ┌─────────────────────────────────────────────────────────────────────┐   │   │
│  │  │              TemperatureControlService                               │   │   │
│  │  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐                  │   │   │
│  │  │  │   Recipe    │ │   Database  │ │   Domain    │                  │   │   │
│  │  │  │  UseCases   │ │   Manager   │ │   Service   │                  │   │   │
│  │  │  └─────────────┘ └─────────────┘ └─────────────┘                  │   │   │
│  │  └─────────────────────────────────────────────────────────────────────┘   │   │
│  └─────────────────────────────────────────────────────────────────────────────┘   │
│                                    │                                              │
└────────────────────────────────────┼──────────────────────────────────────────────┘
                                     │
┌────────────────────────────────────┼──────────────────────────────────────────────┐
│                                DOMAIN LAYER                                      │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │                              Domain Entities                                │   │
│  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐          │   │
│  │  │   Recipe    │ │ Temperature │ │   Control   │ │   Timing    │          │   │
│  │  │   Entity    │ │  Settings   │ │   Settings  │ │   Settings  │          │   │
│  │  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘          │   │
│  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐          │   │
│  │  │     Goal    │ │   Recipe    │ │   Value     │ │   Domain    │          │   │
│  │  │   Settings  │ │   Metadata  │ │   Objects   │ │   Services  │          │   │
│  │  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘          │   │
│  └─────────────────────────────────────────────────────────────────────────────┘   │
│                                    │                                              │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │                              Domain Services                                │   │
│  │  ┌─────────────────────────────────────────────────────────────────────┐   │   │
│  │  │                    RecipeDomainService                               │   │   │
│  │  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐                  │   │   │
│  │  │  │ Validation  │ │   Business  │ │   Rules     │                  │   │   │
│  │  │  │   Logic     │ │   Logic     │ │   Engine    │                  │   │   │
│  │  │  └─────────────┘ └─────────────┘ └─────────────┘                  │   │   │
│  │  └─────────────────────────────────────────────────────────────────────┘   │   │
│  └─────────────────────────────────────────────────────────────────────────────┘   │
│                                    │                                              │
└────────────────────────────────────┼──────────────────────────────────────────────┘
                                     │
┌────────────────────────────────────┼──────────────────────────────────────────────┐
│                              INFRASTRUCTURE LAYER                               │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │                              Modbus Service                                 │   │
│  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐          │   │
│  │  │   Core      │ │ Commands    │ │   Reader    │ │ Performance │          │   │
│  │  │  Service    │ │   System    │ │   Service   │ │   Monitor   │          │   │
│  │  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘          │   │
│  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐          │   │
│  │  │   Recovery  │ │   Parser    │ │   Priority  │ │   Ring      │          │   │
│  │  │   Service   │ │   Service   │ │   Queues    │ │   Buffer    │          │   │
│  │  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘          │   │
│  └─────────────────────────────────────────────────────────────────────────────┘   │
│                                    │                                              │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │                              RabbitMQ Client                                │   │
│  │  ┌─────────────────────────────────────────────────────────────────────┐   │   │
│  │  │                    NexusCore Client                                  │   │   │
│  │  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐                  │   │   │
│  │  │  │ Connection  │ │   Message   │ │   Command   │                  │   │   │
│  │  │  │   Manager   │ │   Handler   │ │   Processor │                  │   │   │
│  │  │  └─────────────┘ └─────────────┘ └─────────────┘                  │   │   │
│  │  └─────────────────────────────────────────────────────────────────────┘   │   │
│  └─────────────────────────────────────────────────────────────────────────────┘   │
│                                    │                                              │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │                              Database Layer                                 │   │
│  │  ┌─────────────────────────────────────────────────────────────────────┐   │   │
│  │  │                        SQLite Database                               │   │   │
│  │  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐                  │   │   │
│  │  │  │   Recipe    │ │   Admin     │ │   System    │                  │   │   │
│  │  │  │   Table     │ │   Settings  │ │   Logs      │                  │   │   │
│  │  │  └─────────────┘ └─────────────┘ └─────────────┘                  │   │   │
│  │  └─────────────────────────────────────────────────────────────────────┘   │   │
│  └─────────────────────────────────────────────────────────────────────────────┘   │
│                                    │                                              │
└────────────────────────────────────┼──────────────────────────────────────────────┘
                                     │
┌────────────────────────────────────┼──────────────────────────────────────────────┐
│                              EXTERNAL SYSTEMS                                    │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │                              ESP32 Device                                   │   │
│  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐          │   │
│  │  │   Modbus    │ │ Temperature │ │   Output    │ │   Input     │          │   │
│  │  │    RTU      │ │   Sensors   │ │   Control   │ │   Sensors   │          │   │
│  │  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘          │   │
│  └─────────────────────────────────────────────────────────────────────────────┘   │
│                                    │                                              │
│  ┌─────────────────────────────────────────────────────────────────────────────┐   │
│  │                              RabbitMQ Server                                │   │
│  │  ┌─────────────────────────────────────────────────────────────────────┐   │   │
│  │  │                        NexusCore Server                               │   │   │
│  │  │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐                  │   │   │
│  │  │  │   Machine   │ │   Partner   │ │   Command   │                  │   │   │
│  │  │  │   Commands  │ │   Updates   │ │   Queue     │                  │   │   │
│  │  │  └─────────────┘ └─────────────┘ └─────────────┘                  │   │   │
│  │  └─────────────────────────────────────────────────────────────────────┘   │   │
│  └─────────────────────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────────────────────┘
```

## 🔄 Data Flow Diagram

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   ESP32 Device  │    │   Frontend UI   │    │   RabbitMQ      │
│                 │    │                 │    │   Server        │
└─────────┬───────┘    └─────────┬───────┘    └─────────┬───────┘
          │                      │                      │
          │ Modbus RTU           │ Wails Events         │ AMQP
          │ Serial Comm          │ (WebSocket-like)     │ TCP/IP
          │                      │                      │
          ▼                      ▼                      ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           MODBUS SERVICE LAYER                              │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐            │
│  │   Serial Port   │  │   Command       │  │   Data          │            │
│  │   Manager       │  │   Queue         │  │   Parser       │            │
│  └─────────┬───────┘  └─────────┬───────┘  └─────────┬───────┘            │
│            │                    │                    │                    │
│            ▼                    ▼                    ▼                    │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐            │
│  │   Connection    │  │   Priority      │  │   Register      │            │
│  │   Handler       │  │   Processor     │  │   Buffer        │            │
│  └─────────┬───────┘  └─────────┬───────┘  └─────────┬───────┘            │
└────────────┼─────────────────────┼────────────────────┼────────────────────┘
             │                     │                     │
             ▼                     ▼                     ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           APPLICATION LAYER                                 │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐            │
│  │   Temperature   │  │   Recipe        │  │   Monitoring    │            │
│  │   Controller    │  │   Manager       │  │   Service       │            │
│  └─────────┬───────┘  └─────────┬───────┘  └─────────┬───────┘            │
│            │                    │                    │                    │
│            ▼                    ▼                    ▼                    │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐            │
│  │   Safety        │  │   Validation    │  │   Performance   │            │
│  │   Monitor       │  │   Engine        │  │   Optimizer     │            │
│  └─────────┬───────┘  └─────────┬───────┘  └─────────┬───────┘            │
└────────────┼─────────────────────┼────────────────────┼────────────────────┘
             │                     │                     │
             ▼                     ▼                     ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           DOMAIN LAYER                                      │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐            │
│  │   Recipe        │  │   Temperature   │  │   Control       │            │
│  │   Entity        │  │   Settings      │  │   Rules         │            │
│  └─────────┬───────┘  └─────────┬───────┘  └─────────┬───────┘            │
│            │                    │                    │                    │
│            ▼                    ▼                    ▼                    │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐            │
│  │   Business      │  │   Validation    │  │   Domain        │            │
│  │   Logic         │  │   Rules         │  │   Services      │            │
│  └─────────┬───────┘  └─────────┬───────┘  └─────────┬───────┘            │
└────────────┼─────────────────────┼────────────────────┼────────────────────┘
             │                     │                     │
             ▼                     ▼                     ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                        INFRASTRUCTURE LAYER                                 │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐            │
│  │   SQLite        │  │   File          │  │   Network       │            │
│  │   Database      │  │   System        │  │   Services      │            │
│  └─────────┬───────┘  └─────────┬───────┘  └─────────┬───────┘            │
│            │                    │                    │                    │
│            ▼                    ▼                    ▼                    │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐            │
│  │   Recipe        │  │   Configuration │  │   External      │            │
│  │   Repository    │  │   Files         │  │   APIs          │            │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘            │
└─────────────────────────────────────────────────────────────────────────────┘
```

## 🔄 Real-time Data Flow Process

### 1. **ESP32 Communication Flow**
```
ESP32 Device → Serial Port → Modbus Service → Application Layer → Frontend UI
     ↑                                                              ↓
     └─────────────── Register Updates ←─── Event Emission ←────────┘
```

**Detailed Steps:**
1. **ESP32** continuously sends Modbus RTU data via serial communication
2. **Modbus Service** reads input registers (0-17) every 50-1000ms (adaptive)
3. **Data Parser** converts raw bytes to structured `RegisterData`
4. **Ring Buffer** stores last 10 readings for data history
5. **Event System** emits `register-data-update` to frontend
6. **Frontend** updates all monitoring panels in real-time

### 2. **Command Execution Flow**
```
Frontend UI → Application Layer → Command Queue → Modbus Service → ESP32 Device
     ↑                                                                    ↓
     └─────────────── Status Update ←─── Event Emission ←─── Response ───┘
```

**Priority Levels:**
- **Emergency (5ms)**: Emergency stop, safety commands
- **High (20ms)**: Critical temperature changes
- **Normal (50ms)**: Regular output control
- **Low (100ms)**: Background operations

### 3. **Recipe Management Flow**
```
Database → Repository → Use Cases → Domain Service → Application → Frontend
     ↑                                                                    ↓
     └─────────────── Recipe Updates ←─── Event Emission ←─── UI Changes ─┘
```

**Process:**
1. **SQLite Database** stores recipe configurations
2. **Repository Layer** provides data access abstraction
3. **Use Cases** implement business logic
4. **Domain Service** validates business rules
5. **Application Layer** coordinates operations
6. **Frontend** displays and manages recipes

### 4. **Safety Monitoring Flow**
```
Temperature Data → Safety Monitor → Alert System → Frontend → User Action
     ↑                                                                    ↓
     └─────────────── Emergency Stop ←─── Safety Rules ←─── Thresholds ───┘
```

**Safety Features:**
- **Temperature Limits**: Configurable thresholds for each zone
- **Emergency Stop**: Immediate shutdown of all outputs
- **Real-time Alerts**: Visual and system notifications
- **Automatic Recovery**: Error detection and recovery

## 🏗️ Layer Responsibilities

### **Presentation Layer (Frontend)**
- **Vue.js Components**: Modular, reusable UI components
- **Real-time Updates**: WebSocket-like event handling via Wails
- **User Interaction**: Forms, controls, and data visualization
- **Responsive Design**: Glass morphism UI with Tailwind CSS

### **Application Layer (Go)**
- **Orchestration**: Coordinates between different services
- **Business Logic**: Implements use cases and workflows
- **Event Management**: Handles communication between layers
- **Configuration**: Manages system settings and preferences

### **Domain Layer (Go)**
- **Business Entities**: Recipe, Temperature, Control objects
- **Business Rules**: Validation logic and constraints
- **Domain Services**: Core business operations
- **Value Objects**: Immutable data structures

### **Infrastructure Layer (Go)**
- **External Communication**: Modbus, RabbitMQ, Serial
- **Data Persistence**: SQLite database operations
- **System Integration**: File system, network services
- **Hardware Abstraction**: Device drivers and protocols

## 🔧 Key Integration Points

### **Wails Framework Integration**
- **Go Backend** ↔ **Vue.js Frontend** via Wails bindings
- **Real-time Events**: `EventsEmit` and `EventsOn` for live updates
- **Method Binding**: Direct function calls between layers
- **Asset Management**: Embedded frontend in Go binary

### **Modbus RTU Protocol**
- **Serial Communication**: 115200 baud, 8N1 configuration
- **Register Mapping**: 18 input registers, 56 holding registers
- **Priority System**: 4-level command prioritization
- **Error Handling**: Automatic recovery and retry mechanisms

### **RabbitMQ Integration**
- **NexusCore Protocol**: Partner communication system
- **Message Queuing**: Asynchronous command processing
- **Machine Identification**: Unique machine ID for routing
- **Real-time Updates**: Live status and command exchange

## 📊 Performance Characteristics

### **Response Times**
- **Emergency Commands**: 5ms execution
- **High Priority**: 20ms execution
- **Normal Operations**: 50ms execution
- **Background Tasks**: 100ms execution

### **Data Throughput**
- **Reading Speed**: 50-1000ms intervals (adaptive)
- **Write Operations**: 5ms interruption time
- **Buffer Size**: 10-slot ring buffer
- **Parallel Operations**: Input and holding registers simultaneously

### **Scalability Features**
- **Modular Architecture**: Easy to extend and maintain
- **Service Separation**: Independent service scaling
- **Event-Driven**: Asynchronous processing
- **Resource Management**: Efficient memory and CPU usage

This architecture provides a robust, scalable, and maintainable foundation for industrial temperature control applications with real-time performance and comprehensive monitoring capabilities.
