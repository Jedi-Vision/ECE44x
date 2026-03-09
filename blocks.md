# Block Designs

## Black-Box System
```mermaid
graph LR
     %% System input and outputs
    camera_nano_envin --> Jedi
    env_struc_envin --> Jedi
    %%usr_in --> Jedi
    Jedi --> amp_out_vibration_asig
    Jedi --> safety_out
    Jedi --> env_struc_envout
```

## Top-Level System
```mermaid
graph TD
    subgraph System
        subgraph Enclosure
            %% Power management block
            sys_input -- "camera_nano_envin" --> Nano

            %% Camera
            %%sys_input -- "environ_in" --> camera
            %%camera -- "cam_feed" --> Nano

            %% Mode Switch
            %%sys_input -- "usr_in" --> mode_switch
            %%mode_switch -- "det_mode" --> Nano

            %% System Health Monitor
            sys_health_monitor_mc <-- "shs_nano_comm" --> Nano

            Nano -- "nano_dac_comm" --> audio_dac
            audio_dac -- "dac_amp_asig" --> amp_vibration
            %%sys_input -- "usr_in" --> Nano
            amp_vibration -- "amp_out_vibration_asig" --> sys_out

        end

        %% System input and outputs
        camera_nano_envin --> Enclosure
        %%usr_in --> Enclosure
        Enclosure --> amp_out_vibration_asig
        Enclosure --> safety_out
        env_struc_envin --> Enclosure
        Enclosure --> env_struc_envout
    end
```

```mermaid
graph TD
    subgraph System

        %% Code
        subgraph Nano
            code_in -- "camera_nano_envin" --> cam_input_process
            code_in -- "shs_nano_comm" --> sys_management
            cam_input_process -- "cam_proc_video_feed_data" --> object_representation
            cam_input_process -- "cam_proc_video_feed_data" --> scene_creation
            object_representation -- "object_rep_object_buff_data" --> object_buffer
            scene_creation -- "scene_creation_object_buff_data" --> object_buffer
            object_buffer -- "object_buff_audio_info_data" --> object_to_audio_info
            sys_management -- "sys_management_info" --> sys_info_to_audio
            sys_info_to_audio -- "sys_mng_info_audio_info_track" --> audio_info_to_spatial_audio
            object_to_audio_info -- "audio_info_spatial_audio_data" --> audio_info_to_spatial_audio
            audio_info_to_spatial_audio -- "nano_usb_spatial_audio_data" --> code_out
        end
    end
```

### Individual Blocks

#### Power Management Block: USB power to battery management
```mermaid
graph LR
    in -- "dc_pwr_in" --> MC
    MC -- "pwr" --> BATT
    MC -- "batt_perc_info" --> out
    BATT -- "barrel_pwr_out" --> out
```

#### Mode Switch Block
```mermaid
graph LR
    in -- "usr_in" --> mode_switch
    mode_switch -- "detect_mode" --> out
```

#### Camera Block
```mermaid
graph LR
    in -- "dc_pwr" --> camera
    in -- "environ_in" --> camera
    camera -- "cam_feed" --> out
```

#### Camera Input Processing Block
```mermaid
graph LR
    in -- "cam_feed" --> cam_input_process
    cam_input_process -- "cam_proc_video_feed_representation" --> out
```

#### Visual to Scene Block
```mermaid
graph LR
    in -- "cam_proc_video_feed_representation" --> scene_model
    scene_model -- "scene" --> out
```

#### Path Representation Block
```mermaid
graph LR
    in -- "scene" --> path_rep_model
    path_rep_model -- "object_scene" --> out
```

#### Object Representation Block
```mermaid
graph LR
    in -- "cam_proc_video_feed_data" --> object_rep_model
    object_rep_model -- "object_rep_object_buffer_data" --> out
```

#### Object Buffer Block
```mermaid
graph LR
    in -- "object_rep_object_buff_data" --> object_buffer
    object_buffer -- "object_buff_audio_info_data" --> out
```

#### Scene to Audio Info Block
```mermaid
graph LR
    in -- "scene" --> scene_proc_model
    scene_proc_model -- "audio_info" --> out
```

#### Audio Info to Spatial Audio Block
```mermaid
graph LR
    in -- "audio_info" --> spatial_encoding
    spatial_encoding -- "spatial_audio" --> spatial_decode
    spatial_decode -- "binaural_audio" --> bc_dsp_conversion
    bc_dsp_conversion -- "usb_spatial_audio" --> out
```

#### Audio Ddc Block: USB audio to analog
```mermaid
graph LR
    in -- "usb_audio" --> usb_audio_dac
    in -- "pwr_in" --> usb_audio_dac
    usb_audio_dac -- "analog_audio" --> out
```

#### Stereo Amp Block: Surround analog audio to L/R audio
```mermaid
graph LR
    in -- "analog_audio" --> stereo_amp
    in -- "pwr_in" --> stereo_amp
    in -- "usr_input" --> slider
    slider -- "volume_control" --> stereo_amp
    stereo_amp -- "right_ch_analog_audio" --> out
    stereo_amp -- "left_ch_analog_audio" --> out
```

#### Transducer Block: L/R audio to vibrations
```mermaid
graph LR
    in -- "right_ch_analog_audio" --> right_transducer
    in -- "left_ch_analog_audio" --> left_transducer

    left_transducer -- "left_vibration" --> out
    right_transducer -- "right_vibration" --> out
```