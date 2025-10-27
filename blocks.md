# Block Designs

## Black-Box System
```mermaid
graph LR
     %% System input and outputs
    dc_pwr_in --> Jedi
    environ_in --> Jedi
    usr_in --> Jedi
    Jedi --> vibration_out
    Jedi --> safety_out
```

## Top-Level System
```mermaid
graph TD
    subgraph System
        subgraph Jedi
            %% Power management block
            sys_input -- "dc_pwr_in" --> pwr_management
            pwr_management -- "pwr_nano_info" --> CodeBlocks
            pwr_management -- "dc_pwr" --> CodeBlocks
            pwr_management -- "dc_pwr" --> amp_vibration
            pwr_management -- "dc_pwr" --> audio_dac

            %% Camera
            %%sys_input -- "environ_in" --> camera
            %%camera -- "cam_feed" --> CodeBlocks

            %% Mode Switch
            %%sys_input -- "usr_in" --> mode_switch
            %%mode_switch -- "det_mode" --> CodeBlocks

            CodeBlocks -- "nano_dac_comm" --> audio_dac
            audio_dac -- "dac_amp_asig" --> amp_vibration
            sys_input -- "usr_in" --> CodeBlocks
            amp_vibration -- "amp_out_vibration" --> sys_out

        end

        %% System input and outputs
        dc_pwr_in --> Jedi
        environ_in --> Jedi
        usr_in --> Jedi
        Jedi --> vibration_out
        Jedi --> safety_out
    end
```

```mermaid
graph TD
    subgraph System

        %% Code
        subgraph CodeBlocks
            code_in -- "env_cam_feed" --> cam_input_process
            code_in -- "pwr_nano_info" --> sys_management
            cam_input_process -- "cam_proc_video_feed_representation" --> object_representation
            cam_input_process -- "cam_proc_video_feed_representation" --> environment_representation
            cam_input_process -- "cam_proc_video_feed_representation" --> scene_creation
            object_representation -- "object_rep_audio_info_mask_coords" --> scene_to_audio_info
            environment_representation -- "object_rep_audio_info_mask_coords" --> scene_to_audio_info
            scene_creation -- "scene_audio_info_rep" --> scene_to_audio_info
            sys_management -- "sys_management_info" --> sys_info_to_audio
            sys_info_to_audio -- "sys_info_audio_info_track" --> audio_info_to_spatial_audio
            scene_to_audio_info -- "audio_info" --> audio_info_to_spatial_audio
            audio_info_to_spatial_audio -- "usb_spatial_audio" --> code_out
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
    in -- "scene" --> object_rep_model
    object_rep_model -- "object_scene" --> out
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