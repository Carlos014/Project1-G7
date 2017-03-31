# Project1-G7
First EA076 project.
Esse é o projeto de um semáforo para o grupo 7 de EA076 - Faculdade de Engenharia Elétrica - Unicamp

/* ###################################################################
**     Filename    : Events.c
**     Project     : dessavaidarcerto
**     Processor   : MKL25Z128VLK4
**     Component   : Events
**     Version     : Driver 01.00
**     Compiler    : GNU C Compiler
**     Date/Time   : 2017-03-16, 15:49, # CodeGen: 0
**     Abstract    :
**         This is user's event module.
**         Put your event handler code here.
**     Settings    :
**     Contents    :
**         Cpu_OnNMIINT - void Cpu_OnNMIINT(void);
**
** ###################################################################*/
/*!
** @file Events.c
** @version 01.00
** @brief
**         This is user's event module.
**         Put your event handler code here.
*/         
/*!
**  @addtogroup Events_module Events module documentation
**  @{
*/         
/* MODULE Events */

#include "Cpu.h"
#include "Events.h"

#ifdef __cplusplus
extern "C" {
#endif 

int s=0, p=0, standard=1, dia=0, nightmode=0;
extern int exportvalue;

/* User includes (#include below this line is not maintained by Processor Expert) */

/*
** ===================================================================
**     Event       :  Cpu_OnNMIINT (module Events)
**
**     Component   :  Cpu [MKL25Z128LK4]
*/
/*!
**     @brief
**         This event is called when the Non maskable interrupt had
**         occurred. This event is automatically enabled when the [NMI
**         interrupt] property is set to 'Enabled'.
*/
/* ===================================================================*/
void Cpu_OnNMIINT(void)
{
	/* Write your code here ... */
}

/*
 ** ===================================================================
 **     Event       :  TI1_OnInterrupt (module Events)
 **
 **     Component   :  TI1 [TimerInt]
 **     Description :
 **         When a timer interrupt occurs this event is called (only
 **         when the component is enabled - <Enable> and the events are
 **         enabled - <EnableEvent>). This event is enabled only if a
 **         <interrupt service/event> is enabled.
 **     Parameters  : None
 **     Returns     : Nothing
 ** ===================================================================
 */

int a = 0;
int b = 0;

void TI1_OnInterrupt(void)
{
	if (exportvalue>40000) dia++;
	else dia--;
	
	
	if(dia==10 && nightmode==1){
		dia=0;
		nightmode=0;
		standard=1;
	}
	if(dia==-10 && nightmode==0){
		dia=0;
		nightmode=1;
		SemRed_ClrVal();
		SemYel_ClrVal();
		SemGreen_ClrVal();
		PedRed_ClrVal();
		PedGreen_ClrVal();
	}
	
	if (nightmode){
		SemYel_NegVal();
		PedRed_NegVal();
	}
	
	if(!nightmode) {
		//utilizamos uma variável "standard" para o estado padrão, que seria sinal de carros aberto
		//e sinal de pedestres fechado.
		if (standard) {
			SemRed_ClrVal();
			PedGreen_ClrVal();

			SemGreen_SetVal();
			PedRed_SetVal();
			standard = 0;
		}

		//p é a variável da interrupção do push button. Ao ser pressionada uma vez, executa essa rotina.
		if (p){

			//s representa a contagem de tempo, que queremos cada estado do programa.
			s++;

			if (s==9) {
				SemYel_SetVal();
				SemGreen_ClrVal();
			}	

			if (s==11){
				PedRed_ClrVal();
				SemYel_ClrVal();
				SemRed_SetVal();
				PedGreen_SetVal();
			}

			if (s==16)PedGreen_ClrVal();
			if (s>=16)PedRed_NegVal(); //Pisca semáforo vermelho do pedestre.

			//ao finalizar a rotina, reseta a contagem de tempo e a variável de interrupção,
			//retornando ao estado padrão.
			if (s==20){ 
				s=0;
				p=0;
				standard = 1;
			}
		}
	}
}
/*
** ===================================================================
**     Event       :  Pushbutton_OnInterrupt (module Events)
**
**     Component   :  Pushbutton [ExtInt]
**     Description :
**         This event is called when an active signal edge/level has
**         occurred.
**     Parameters  : None
**     Returns     : Nothing
** ===================================================================
*/
void Pushbutton_OnInterrupt(void)
{
  /* Write your code here ... */
	p=1;
}

/*
** ===================================================================
**     Event       :  AD1_OnEnd (module Events)
**
**     Component   :  AD1 [ADC]
**     Description :
**         This event is called after the measurement (which consists
**         of <1 or more conversions>) is/are finished.
**         The event is available only when the <Interrupt
**         service/event> property is enabled.
**     Parameters  : None
**     Returns     : Nothing
** ===================================================================
*/
void AD1_OnEnd(void)
{
  /* Write your code here ... */
	
}

/*
** ===================================================================
**     Event       :  AD1_OnCalibrationEnd (module Events)
**
**     Component   :  AD1 [ADC]
**     Description :
**         This event is called when the calibration has been finished.
**         User should check if the calibration pass or fail by
**         Calibration status method./nThis event is enabled only if
**         the <Interrupt service/event> property is enabled.
**     Parameters  : None
**     Returns     : Nothing
** ===================================================================
*/
void AD1_OnCalibrationEnd(void)
{
  /* Write your code here ... */
}

/* END Events */

#ifdef __cplusplus
}  /* extern "C" */
#endif 

/*!
** @}
*/
/*
** ###################################################################
**
**     This file was created by Processor Expert 10.3 [05.09]
**     for the Freescale Kinetis series of microcontrollers.
**
** ###################################################################
*/
