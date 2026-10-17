# [Join the Planet for Free] Basics of Link11 Signal, Signal Styles, and MATLAB Simulation [with MATLAB Code]

Link 11 is a confidential, networked digital data link used for the exchange of tactical digital information between ships, ship-shore, ship-air, and air-shore. The data link employs M sequence messages, standardized by the U.S. Military Standard MIL-STD-6011 and NATO Standard STANAG5511. The communication standard is defined by the U.S. Military Standard MIL-STD-188-203-1A. The standard transmission rate is either 1364bps or 2250bps. It typically operates in a polling mode under the control of a network control station, but can also operate in a broadcast mode. Link 11 data links use both HF and UHF bands.When using the HF band, it can cover an area of 300nmile around the location of information transmission; when using the UHF band, it can provide coverage for ship-to-ship at 25nmile and ship-to-air at 150nmile.
The Link 11 data link was put into use in the 1970s, a networked digital data link that is confidential. The so-called Link 11 data link, apart from the sea-based Link 11 data link, also includes the land-based Link 11B data link, which corresponds to the U.S. military's TADIL-A and TADIL-B. The Link 11B is primarily used for land-based air defense and relative to the sea-based Link 11, the application range of Link 11B is smaller. This paper mainly focuses on the sea-based Link 11 data link (hereinafter referred to as the Link 11 data link). The Link 11 data link is primarily used for achieving tactical digital information exchange between ships, shore installations, ship-air, and air-shore.The data link adopts a M-sequence message work mode, which includes six polling modes: network synchronization (Net Sync), network testing (Net Test), polling, broadcasting, short broadcasting, and radio silence. These are the main working modes of the Link11 data link. The Link11 data link uses both HF and UHF bands when operating on the HF band, it can cover an area within 300nmile around the transmitting location; when operating on the UHF band, it can provide coverage for ship-to-ship at 25nmile and ship-to-air at 150nmile.
The typical Link11 data link system configuration is shown in Figure 1, consisting of a computer, confidential equipment, data terminals (DTS), HF or UHF radio devices. The DTS is connected to the radio devices to complete all control functions related to network operations.
The implementation principle of the algorithm follows the Link11 data link communication standard [1]. The data terminal is the core of the Link11 data link and performs the generation and reception detection of Link11 audio signals. Therefore, the physical layer algorithms of the Link11 data terminal mainly refer to the physical layer algorithms of the Link11 data terminal. Below is a detailed analysis of the functions of the data terminal.
(1) Coding
The coding method used by Link11 is Hamming coding, which adds some additional bits after each information coded group to achieve error correction and error detection purposes. The DTS receives 24-bit binary data from the TDS computer, then adds 6 bits of error correction data (EDAC) to expand the 24-bit data into 30 bits. The value is based on specific parity checking relationships for the 24-bit data word:
In the Link11 system, only the data information is coded with Hamming codes, starting codes, termination codes, and address codes are not coded with Hamming codes.
(2) Modulation and demodulation
In the Link11 system, the audio signal is divided into data frames to indicate each 30-bit data block. In these 15 audio data frames, each audio's phase shift conveys the digital information contained within it. Each frame's phase shift is determined by the previous frame according to the phase difference, which determines the value of these two bits of data. The phase change follows the rules in Table 1.
The physical layer process of the data terminal has been modularized as shown in Figures 2 and 3, where the dashed boxes represent the physical layer process of the data terminal. Except for the AD and DA conversion modules, all other modules require algorithm design. Below is an analysis of the main algorithmic modules according to the flow from sending to receiving signals.
Figure 2 Signal Sending Process
Figure 3 Signal Receiving and Detection Process
1. The tactical message of Link11 channel coding uses the Hamming code scheme with a 30-24 bit pattern defined by the Link11 data link system communication standard [1]. From this, it can be deduced that the generation matrix G is 24×39. By transmitting the tactical message information n×24 through the generation matrix G, we obtain n frames of 30-bit parallel data:

2. The Link11 data link system adopts a multi-tone parallel system without a channel estimation mechanism. Therefore, it can only use the relative phase shift modulation - Differential Quadrature Phase Shift Keying (π/4-DQPSK) to solve the problem of phase ambiguity affecting coherent demodulation output. Firstly, generate DQPSK symbols for baseband and then convert them to audio. The signal can be represented as:

In the formula:
Ik, Qk are the symbol pairs output this time; Ik-1, Qk-1 are the symbol pairs output last time; θk is the phase change corresponding to the input binary pair.

3. The Link11 data link system employs a multi-tone parallel system, which means using 15 audio carriers to simultaneously transmit signals. The calculation of a DQPSK symbol pair (Ik, Qk) to its corresponding frequency's discrete audio modulation signal is as follows:

If we let dk = Ik + jQk, then s(fcnTs) = Re(dkej2πfcnTs), so N single-tone DQPSK discrete audio modulation signals can be synthesized:

Let N = 15, and fk corresponds to the 15 data subcarrier single-tones specified in the communication standard, then equation (10) is the Link11 audio signal (discrete). After performing multi-tone DQPSK symbol pair modulation on n×15 parallel DQPSK symbols, we can obtain the discrete sequence of audio modulation signals.
(4) Subcarrier demodulation: The subcarrier demodulation completes the transformation of multi-tone parallel signals to baseband. Output DQPSK symbol pairs. The analytical form of equation (10) is rewritten through mathematical transformation as follows:
In the formula: ( fk - f0) Ts = k /N, where k represents the corresponding frequency point number.
The expression for the decoding formula of the DQPSK symbol pair dk carrying data information can be represented as:
Link-11 signal style - Link-11 can be transmitted using either single sideband or double sideband modulation, depending on propagation conditions. The double sideband provides more signal reception redundancy for the receiving station. The receiving station can decide which sideband to receive based on the signal quality.
Figure 4: Single-sideband and Double-Sideband Waterfall Diagram
There are two main waveform modes: Traditional Link 11 Waveform (CLEW) and Single-tone Link 11 Waveform (SLEW).
Figure 5 CLEW (DQPSK) vs. SLEW (8-PSK)
Link11 currently detected signal frequency
LINK11SignalMATLABSimulationclc\nclose all\nclear all\n​\nBit_R = 2250; % Bit Rate\nSym_R = 75; % Symbol Rate = 2250 bps / log2(M) / (nCarrs-1) , M for DQPSK Alphabet Size, nCarrs = 16;\n% BB_R = 2.25e3; % Baseband Rate (Spreaded Symbol Rate)\n% IF_R = 2.25e5; % IF Rate\n% Center_Freq = 7e7; % Carrier Freq\nSample_R = 12000; % Sample Rate\n​\nnCarrs = 16; % 16 Tone\nfBase = 55; % Each 15 SubCarrier = int * fbase\n[fCarrs, fSpace] = carrierG( nCarrs, fBase ); % 16 Tone Carriers Freq and Freq-Interval\n​\nk = 0 : 2399; % Store Points\nI = cos(2 * pi * fBase * 1 / Sample_R * k);\nQ = cos(2 * pi * fBase * 1 / Sample_R * k);\n​\nDBit_Num = 5e3;\nDFrm_Num = floor(DBit_Num / 24); % 30-bit word = 24-bit data word + 6 Hamming parity bits in each frame\n%Frm_Len = 15; % 15 Tones with DQPSK = 30 bits/frame\n​\nM=4; % DQPSK\n​\n% RCCF Configuration\nOverSamNum = 5; % Samples Per Symbol = 5\nSybOrdNum = 6; % Filter Order in Symbols\nR_fircos = 0.25; % Rolloff Factor\n​\n%% Frames Generation\nPre_Frms = PreFrm_Gen( Sample_R, Bit_R, fSpace, I, Q ); % Preamble Frames Generation\n​\n[PhaRef_Frm, Pha_Ref] = PhaRef_Gen(Sample_R, Bit_R, nCarrs, fSpace, I, Q, M); %Phase Reference Frame Generation\n​\n% Control Code Frames Generation\nStart_Frms = CCFrm_Gen(Pha_Ref, Sample_R, Bit_R, nCarrs, fSpace, I, Q, M, 'STARTC'); % Start Code\nPStop_Frms = CCFrm_Gen(Pha_Ref, Sample_R, Bit_R, nCarrs, fSpace, I, Q, M, 'PSTOPC'); % Picket Stop Code\nCStop_Frms = CCFrm_Gen(Pha_Ref, Sample_R, Bit_R, nCarrs, fSpace, I, Q, M, 'CSTOPC'); % Control Stop Code\n​\nOrigin_Bits = Data_BitsG(DFrm_Num); % 30-bit word-by-word\n% Data Bit Generation; 15-Tone Composite Signal with  2-bit/tone\nData_Frms = DFrm_Gen(Origin_Bits, Pha_Ref, Sample_R, Bit_R, nCarrs, fSpace, I, Q, M);% Data Frame Generation\n​\n%% Framing\nIwM = [Pre_Frms PhaRef_Frm Start_Frms Data_Frms CStop_Frms CStop_Frms]; % Interrogation with a Message\nI = [Pre_Frms PhaRef_Frm]; % Interrogation\nPR = [Pre_Frms PhaRef_Frm Start_Frms Data_Frms PStop_Frms PStop_Frms]; % Picket Reply\n​\ny = UHF_dds( IwM );\n​\n  

## Images

![(Join the Planet for Free)Link11 Signal Fundamentals, Signal Styles, and MATLAB Simulation [Included MATLAB Code]](01.jpg)
![(Join the Planet for Free)Link11 Signal Fundamentals, Signal Styles, and MATLAB Simulation [Included MATLAB Code]](02.jpg)
![(Join the Planet for Free)Link11 Signal Fundamentals, Signal Styles, and MATLAB Simulation [Included MATLAB Code]](03.jpg)
![(Join the Planet for Free)Link11 Signal Fundamentals, Signal Styles, and MATLAB Simulation [Included MATLAB Code]](04.jpg)
![(Join the Planet for Free)Link11 Signal Fundamentals, Signal Styles, and MATLAB Simulation [Included MATLAB Code]](05.jpg)
![(Join the Planet for Free)Link11 Signal Fundamentals, Signal Styles, and MATLAB Simulation [Included MATLAB Code]](06.jpg)
![(Join the Planet for Free)Link11 Signal Fundamentals, Signal Styles, and MATLAB Simulation [Included MATLAB Code]](07.jpg)
![(Join the Planet for Free)Link11 Signal Fundamentals, Signal Styles, and MATLAB Simulation [Included MATLAB Code]](08.jpg)
![(Join the Planet for Free)Link11 Signal Fundamentals, Signal Styles, and MATLAB Simulation [Included MATLAB Code]](09.jpg)
![(Join the Planet for Free)Link11 Signal Fundamentals, Signal Styles, and MATLAB Simulation [Included MATLAB Code]](10.jpg)
![(Join the Planet for Free)Link11 Signal Fundamentals, Signal Styles, and MATLAB Simulation [Included MATLAB Code]](11.jpg)
![(Join the Planet for Free)Link11 Signal Fundamentals, Signal Styles, and MATLAB Simulation [Included MATLAB Code]](12.jpg)
![(Join the Planet for Free)Link11 Signal Fundamentals, Signal Styles, and MATLAB Simulation [Included MATLAB Code]](13.jpg)
![(Join the Planet for Free)Link11 Signal Fundamentals, Signal Styles, and MATLAB Simulation [Included MATLAB Code]](14.jpg)
![(Join the Planet for Free)Link11 Signal Fundamentals, Signal Styles, and MATLAB Simulation [Included MATLAB Code]](15.jpg)
![(Join the Planet for Free)Link11 Signal Fundamentals, Signal Styles, and MATLAB Simulation [Included MATLAB Code]](16.jpg)
![(Join the Planet for Free)Link11 Signal Fundamentals, Signal Styles, and MATLAB Simulation [Included MATLAB Code]](17.jpg)
![(Join the Planet for Free)Link11 Signal Fundamentals, Signal Styles, and MATLAB Simulation [Included MATLAB Code]](18.jpg)
![(Join the Planet for Free)Link11 Signal Fundamentals, Signal Styles, and MATLAB Simulation [Included MATLAB Code]](19.jpg)

<!-- PAYMENT_BLOCK -->

## Get this dataset

- Payment link (Stripe): [Buy now](https://buy.stripe.com/3cs8yP7sY87d0vu9AB)
- Payment QR code: ![Payment QR code](pay.png)

<!-- /PAYMENT_BLOCK -->
