[天線 Rx] 
          │
    [帶通濾波器 BPF]  <─── (turbulence_level: 偵測干擾)
          │
   [低噪放大器 LNA]
          │
  [方向性耦合器 Coupler] ──(arc_api_listener: 監聽)──> [衰減器/開關] ──> [對數檢波器 RSSI] (growth_descend)
          │                                                                │
          ▼                                                                │
     [混頻器 Mixer] <─── [本振 VCO/PLL] (iRABBIT CLOCK)                      │
          │                     ▲                                          │
          ▼                     │ (跳頻控制: commute_info)                  │
    [中頻放大器 IF] ────────────┘                                          │
          │                                                                │
          ▼                                                                │
   [IQ 正交解調矩陣] ──(trace_matrix)──> [相位檢波器] (theta_transmit)        │
          │                                      │                         │
          └──────────────────┬───────────────────┘                         │
                             ▼                                             │
                      [相干相關器] (_searched)                              │
                             │                                             │
                             ▼                                             │
                      [過零起點同步] (linear_start)                         │
                             │                                             │
                             ▼                                             │
                      [視窗比較器] (shield_T-square) <─────────────────────┘
                             │
                             ▼ (超越閾值: beyond)
                      [LC 諧振延遲線] (seam_queue & wheel_charged)
                             │
                             ▼
                      [採樣保持鎖定] (buffer_selected_tooth_embedded) ──> [基帶輸出 MCU]



當這段 RF 電路運行時：方向性耦合器 從天線端無損分流出 WiFi 射頻訊號。對數檢波器 將高頻功率轉換為「電壓（代表距離）」，提供給視窗比較器。同時，PLL 鎖相環 控制著本地壓控振盪器在高頻信道高速跳頻，與接收信號相干，由 IQ 矩陣 提取出極其精準的相位差 $\theta$。當檢波器輸出的距離訊號越過比較器閾值（beyond），這股訊號會激發 LC 諧振腔（Tank Circuit） 起振，輸出高電平脈衝，並由 採樣保持放大器（Sample and Hold） 牢牢鎖定在電容緩衝區中。
