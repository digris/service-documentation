# DAB+ Encoder Setup

## 'Minimal' version

### Preparation

 - Install preseed debian
 - Add IP address to the `[encoder_setup]` and `[encoder_setup]` groups in `hosts-encoder`
 - Use `<station-id>.encoder.digris.ch` as hostname
 
To be able to handle the system via ansible the `encoder-initial.yml` playbook has to be applied:

    ansible-playbook --user=odr --ask-pass --become --become-user=root --ask-become-pass --become-method=su \
    -i hosts-encoder -l encoder_setup encoder-initial.yml
    
 - SSH Password: *the usual one* (is preseeded in the Debian image)
 - SU Password & user account: use the provided defaults

 
### Register Encoder

Register the encoder on the Digris service platform: 
[service.digris.ch](https://service.digris.ch/admin/infrastructure/encoder/) 

 - 'Add Encoder'
 - `Encoder ID`: must match the first bit of the `hostname`
 - `Access Token`: if left empty it will be created on 'Save'
 - `Channel`: assign the encoder to a Digris channel
 - 'Save & Continue'
 
#### Service Tunnels

Encoders use a reverse tunnel (SSH) for communication, in the 2*** range, and 5*** range for monitoring. 
Pick the next `free` 2*** port for `Service Tunnel Port` (under Network settings).


### Encoder Installation

Create a new file in `host_vars` directory using the same hostname as above.  
Resp. copy an existing one and adjust the settings.

 - `encoder_id`: must match the first bit of the `hostname`
 - `encoder_access_token`: Use the token from the step above
 - `encoder_outputs`: they correspond to the `-o` resp. `--output` format of 
 [ODR-AudioEnc](https://github.com/Opendigitalradio/ODR-AudioEnc)
 
 
Under `encoder_service_autossh_tunnels`, as `remote_port`s - use a 2*** and 5*** value from above.
 

#### Encoder Playbook 

Now everything should be ready to run the final `encoder.yml` playbook   
You can first do a 'dry-run' using the `--check` option:

    ansible-playbook -i hosts-encoder -l <station-id>.encoder.digris.ch encoder.yml --check

Note:

Make sure no user is logged in as `odr` on the machine. Else the playbook will fail when setting up uid/gid.



### Audioscience Cards


#### Playbook Settings

To enable the Audioscience configuration change the `audio_interface`settings for the respective host:

    encoder_audio_interface: asi5211
    encoder_asihpi_version: 4.14.03
    encoder_audio_interface_input: analog
    encoder_audio_interface_drift_compensation: yes


#### A note on Audioscience cards: 

Here some manual work is eventually required. If used more often there should be a better way to handle this.

The drivers (snd_asihpi) for the card [ASI5211](http://www.audioscience.com/internet/products/sound_cards/asi5111_5211.htm) come
with the Debian kernel.  
However - the firmware must be added manually for now. 

Check the driver version:

    modinfo snd_asihpi
    
Currently this will output `4.10.01` - now the respective firmware has to be placed in `/lib/firmware/asihpi/`  
The firmware binary is `dsp6200.bin`

The files can be downloaded under:  
http://www.audioscience.com/internet/download/drivers/released/
