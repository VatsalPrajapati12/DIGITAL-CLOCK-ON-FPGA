# DIGITAL-CLOCK-ON-FPGA

Tool Used : Xilinx ISE , Spartan 6A
  
  Abstract
  
In recent era of digital world, most peoples in the whole world use an automated digital clock in their everyday
use. Starting from the hand watch we were to those huge street clocks every one of us are
dependent on the display the make. In 21th century time being more important  than money, regarding this
change our hobbies of checking our time every minute is dramatically increasing.  of
today’s digital clocks are made using microcontrollers which make them more hand able from the
rest, those we can set the time to start any minute or second we want and also set an alarm for
reminder so that the system will store the value in a memory and then when the time reaches
the alarm will be on. We are targetting to make the same Digital clock using FPGA in order to understand the working of FPGA.

Objective
Our proposed system will be synthesized in FPGA using VHDL code to make the synchronous counter that counts
the hour, minute and second and also uses four external inputs for hour and minutes and one more external input to reset the clock.

Significance

Digital clocks are being a very useful components of our lives. Regarding this change the need of
accurate and simple materials also dramatically increasing. Our proposed project uses a FPGA to build an accurate synchronous digital clock that is expected to satisfy the need of those materials

Future Scope

In future we can extend its range till the far possible reach having a negligible delay, a setting
buttons and a second display.

Circuit Diagram

Circuit Diagram will be cosisting of total five push buttons at the input of FPGA four push buttons which wil be used for setting hour and minute bits will be configured as pull up resistors and the button which will used for reset input we be connected in pull down configuration.


VHDL Coding


Clock Divider Subsystem

        library IEEE;
        use IEEE.STD_LOGIC_1164.ALL;
        USE IEEE.STD_LOGIC_UNSIGNED.ALL;
              entity clk_div is
                port (
                      clk_50: in std_logic;
                      clk_1s: out std_logic
                      );
              end clk_div;
         
         architecture Behavioral of clk_div is

         signal clk_sig:std_logic:='0';
            begin
              p0: process(clk_50)
              variable cnt:integer:=1;
                begin
                  if rising_edge(clk_50) then
                    if(cnt=5) then   -- for running simulation -- comment when running on FPGA
					 -----if(cnt>=x"2FAF080") then -- for running on FPGA -- comment when running simulation

                   clk_sig<= not(clk_sig);
                   cnt:=1;
                   else
                     cnt:=cnt+1;
                   end if;
                   end if;
                   end process;
               clk_1s<=clk_sig;

          end Behavioral;
       
       
  Binary to Seven segment Display Converter Subsystem    
       
        library IEEE;
        use IEEE.STD_LOGIC_1164.ALL;
        entity bin2ssd is
        port (
              Bin: in std_logic_vector(3 downto 0);
              Hout: out std_logic_vector(6 downto 0)
             );
        end bin2ssd;
        architecture Behavioral of bin2ssd is
         begin
           process(Bin)
             begin
                case(Bin) is
                 when "0000" =>  Hout <= "1000000"; --0--64
                 when "0001" =>  Hout <= "1111001"; --1--121
                 when "0010" =>  Hout <= "0100100"; --2--36
                 when "0011" =>  Hout <= "0110000"; --3--48
                 when "0100" =>  Hout <= "0011001"; --4--25
                 when "0101" =>  Hout <= "0010010"; --5--18    
                 when "0110" =>  Hout <= "0000010"; --6--2
                 when "0111" =>  Hout <= "1111000"; --7--120   
                 when "1000" =>  Hout <= "0000000"; --8--0
                 when "1001" =>  Hout <= "0010000"; --9--16
                 when "1010" =>  Hout <= "0001000"; --a--8
                 when "1011" =>  Hout <= "0000011"; --b--3
                 when "1100" =>  Hout <= "1000110"; --c--70
                 when "1101" =>  Hout <= "0100001"; --d--33
                 when "1110" =>  Hout <= "0000110"; --e--6
                 when others =>  Hout <= "0001110"; ---
               end case;
           end process;
         end Behavioral;

Digital Clock system

        library IEEE;
        use IEEE.STD_LOGIC_1164.ALL;
        use IEEE.numeric_std.all;
        -- VHDL project: VHDL code for digital clock

        entity digital_clock is
              port ( 

               clk_50M: in std_logic; 
               -- clock 50 MHz

               reset_in: in std_logic; 
               -- Active low reset pulse, to set the time to the input hour and minute (as 
               -- defined by the Hour_in1, Hour_in0, Minute_in1, and Minute_in0 inputs) and the second to 00.
               -- For normal operation, this input pin should be 1.

               Hour_in1: in std_logic_vector(1 downto 0);
               -- 2-bit input used to set the most significant hour digit of the clock 
               -- Valid values are 0 to 2. 

               Hour_in0: in std_logic_vector(3 downto 0);
               -- 4-bit input used to set the least significant hour digit of the clock 
               -- Valid values are 0 to 9.  

               Minute_in1: in std_logic_vector(3 downto 0);
               -- 4-bit input used to set the most significant minute digit of the clock 
               -- Valid values are 0 to 9.  

               Minute_in0: in std_logic_vector(3 downto 0);
               -- 4-bit input used to set the least significant minute digit of the clock 
               -- Valid values are 0 to 9.  

               Hour_out1: out std_logic_vector(6 downto 0);
               -- The most significant digit of the hour. Valid values are 0 to 2 ( Hexadecimal value on 7-segment LED)

               Hour_out0: out std_logic_vector(6 downto 0);
               -- The most significant digit of the hour. Valid values are 0 to 9 ( Hexadecimal value on 7-segment LED)

               Minute_out1: out std_logic_vector(6 downto 0);
               -- The most significant digit of the minute. Valid values are 0 to 9 ( Hexadecimal value on 7-segment LED)

               Minute_out0: out std_logic_vector(6 downto 0)
               -- The most significant digit of the minute. Valid values are 0 to 9 ( Hexadecimal value on 7-segment LED)
               );

        end digital_clock;

        architecture Behavioral of digital_clock is

            component bin2ssd
            port (
             Bin: in std_logic_vector(3 downto 0);
             Hout: out std_logic_vector(6 downto 0)
             );
            end component;

            component clk_div
            port (
             clk_50: in std_logic;
             clk_1s: out std_logic
                 );
            end component;

              signal clk_1s: std_logic; -- 1-s clock
              signal cnt_hour, cnt_mnt, cnt_sec: integer;

              -- counter using for create time
              signal Hour_out1_bin: std_logic_vector(3 downto 0); --The most significant digit of the hour
              signal Hour_out0_bin: std_logic_vector(3 downto 0);--The least significant digit of the hour
              signal Minute_out1_bin: std_logic_vector(3 downto 0);--The most significant digit of the minute
              signal Minute_out0_bin: std_logic_vector(3 downto 0);--The least significant digit of the minute

        begin

            -- create 1-s clock --|
            create_1s_clock: clk_div port map (clk_50 => clk_50M, clk_1s => clk_1s); 
          
          -- clock operation ---|
            process(clk_1s,reset_in) begin 

          if(reset_in = '0') then
             cnt_hour <= to_integer(unsigned(Hour_in1))*10 + to_integer(unsigned(Hour_in0));
             cnt_mnt <= to_integer(unsigned(Minute_in1))*10 + to_integer(unsigned(Minute_in0));
             cnt_sec <= 0;

               elsif(rising_edge(clk_1s)) then
               cnt_sec <= cnt_sec + 1;

                 if(cnt_sec >=59) then -- second > 59 then minute increases
                 cnt_mnt <= cnt_mnt + 1;
                 cnt_sec <= 0;

                   if(cnt_mnt >=59) then -- minute > 59 then hour increases
                   cnt_mnt <= 0;
                   cnt_hour <= cnt_hour + 1;

                     if(cnt_hour >= 24) then -- hour > 24 then set hour to 0
                     cnt_hour <= 0;
                     end if;
                    end if;
                  end if;
              end if;
          end process;
        
        -- Conversion time ---|
        
        -- Hour_out1 binary value
          Hour_out1_bin <= x"2" when cnt_hour >=20 else
                           x"1" when cnt_hour >=10 else
                           x"0";
                           
        -- 7-Segment LED display of Hour_out1
        convert_hex_Hour_out1: bin2ssd 
        port map (Bin => Hour_out1_bin, Hout => Hour_out1); 

        -- H_out0 binary value
         Hour_out0_bin <= std_logic_vector(to_unsigned((cnt_hour - to_integer(unsigned(Hour_out1_bin))*10),4));

        -- 7-Segment LED display of H_out0
        convert_hex_H_out0: bin2ssd port map (Bin => Hour_out0_bin, Hout => Hour_out0); 


        -- Minute_out1 binary value
         Minute_out1_bin <= x"5" when cnt_mnt >=50 else
                            x"4" when cnt_mnt >=40 else
                            x"3" when cnt_mnt >=30 else
                            x"2" when cnt_mnt >=20 else
                            x"1" when cnt_mnt >=10 else
                            x"0";


        -- 7-Segment LED display of Minute_out1
        convert_hex_Minute_out1: bin2ssd port map (Bin => Minute_out1_bin, Hout => Minute_out1); 


        -- Minute_out0 binary value
         Minute_out0_bin <= std_logic_vector(to_unsigned((cnt_mnt - to_integer(unsigned(Minute_out1_bin))*10),4));

        -- 7-Segment LED display of Minute_out0

        convert_hex_Minute_out0: bin2ssd port map (Bin => Minute_out0_bin, Hout => Minute_out0); 

        end Behavioral;
        
 
 Testbench for Digital Clock
 
          LIBRARY ieee;
          USE ieee.std_logic_1164.ALL;



          ENTITY tb_digital_clock IS
          END tb_digital_clock;


          ARCHITECTURE behavior OF tb_digital_clock IS 
            -- Component Declaration for the Unit Under Test (UUT)
              COMPONENT digital_clock
              PORT(
                   clk_50M : IN  std_logic;
                   reset_in : IN  std_logic;
                   Hour_in1 : IN  std_logic_vector(1 downto 0);
                   Hour_in0 : IN  std_logic_vector(3 downto 0);
                   Minute_in1 : IN  std_logic_vector(3 downto 0);
                   Minute_in0 : IN  std_logic_vector(3 downto 0);
                   Hour_out1 : OUT  std_logic_vector(6 downto 0);
                   Hour_out0 : OUT  std_logic_vector(6 downto 0);
                   Minute_out1 : OUT  std_logic_vector(6 downto 0);
                   Minute_out0 : OUT  std_logic_vector(6 downto 0)
                  );
              END COMPONENT;

             --Inputs
             signal clk_50M : std_logic := '0';
             signal reset_in : std_logic := '0';
             signal Hour_in1 : std_logic_vector(1 downto 0) := (others => '0');
             signal Hour_in0 : std_logic_vector(3 downto 0) := (others => '0');
             signal Minute_in1 : std_logic_vector(3 downto 0) := (others => '0');
             signal Minute_in0 : std_logic_vector(3 downto 0) := (others => '0');

            --Outputs
             signal Hour_out1 : std_logic_vector(6 downto 0);
             signal Hour_out0 : std_logic_vector(6 downto 0);
             signal Minute_out1 : std_logic_vector(6 downto 0);
             signal Minute_out0 : std_logic_vector(6 downto 0);

             -- Clock period definitions
             constant clk_period : time := 10 ps;
          BEGIN


           -- Instantiate the Unit Under Test (UUT)
             uut: digital_clock PORT MAP (
                    clk_50M => clk_50M,
                    reset_in => reset_in,
                    Hour_in1 => Hour_in1,
                    Hour_in0 => Hour_in0,
                    Minute_in1 => Minute_in1,
                    Minute_in0 => Minute_in0,
                    Hour_out1 => Hour_out1,
                    Hour_out0 => Hour_out0,
                    Minute_out1 => Minute_out1,
                    Minute_out0 => Minute_out0
                  );


             -- Clock process definitions
             clk_process :process
             begin
               clk_50M <= '0';
               wait for clk_period/2;
               clk_50M <= '1';
               wait for clk_period/2;
             end process;
             -- Stimulus process
             stim_proc: process
             begin 
                -- hold reset state for 100 ns.
               reset_in <= '0';
               Hour_in1 <= "01";
               Hour_in0 <= x"0";
               Minute_in1 <= x"2";
               Minute_in0 <= x"0";
               wait for 100 ns; 
               reset_in <= '1';
                wait for clk_period*10;
          -- insert stimulus here 

                wait;
             end process;

          END;


Results          
![image](https://user-images.githubusercontent.com/82579490/121116470-3c45e400-c834-11eb-9774-d7d0ec6caf8b.png)

FIG1:- Simulation results of Digital clock
