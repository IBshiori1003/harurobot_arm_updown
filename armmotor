#include "mbed.h"

#define PWM_T 0.1 //PWM周期：100ms
PwmOut PWM_TRIGER(PB_6); //超音波センサモジュールのTriger端子に入力する信号


InterruptIn GET_PWM(PC_7); //割り込み入力端子の設定．マイコンから出力したPWM信号をD9端子から取り込む． 

Timer ActiveTime; //タイマー計測用変数

//上下運動アームのモーター設定
//ピン出力の設定　モタドラとの接続ピン
PwmOut armdown(PA_6); //0.5出力 で動く、０出力で動かない⇒正回転をし下方向
PwmOut armup(PA_5);//0.5出力 で動く、０出力で動かない⇒逆回転をし上方向
DigitalOut stbyAB(PA_11);
DigitalOut stbyCD(PC_9);
    
/*PWM周期の設定
armup.period(50);
armdown.period(50);
*/

//ボタンのピン設定
DigitalIn buttondown(PC_11,PullUp); //PullUp:押してなければ0,押したら1.
//ボタンを押している間アームが下がる。離すとアームが上がる。

unsigned int Count;

unsigned char Flag;
double dist;

void RiseEcho(){
    ActiveTime.start();
}
 
void FallEcho(){
    unsigned long ActiveWidth;
    ActiveTime.stop();
    ActiveWidth = ActiveTime.read_us();
    dist = ActiveWidth * 0.0170; //音速：0.034cm/us　divided by two
    ActiveTime.reset();
    Flag = 1; //フラグのリセット
}
 
int main(){
    GET_PWM.rise(&RiseEcho);
    GET_PWM.fall(&FallEcho);
    
    PWM_TRIGER.period(PWM_T);
    PWM_TRIGER.write(0.01f);//duty比:1%
    
    Count=0;
    Flag= 0;
    int threshold=100;
    ActiveTime.reset();
    
    while(1){
        if (Flag==1){
            if (dist < threshold){ //測定キョリが基準値よりも小さくなった場合
                armdown=0.5;
                armup=0;
                wait(300);
                armdown=0;
                armup=0.5;
                wait(300);
            }
        else{
                armdown=0;
                armup=0;
            }
        }
        if(buttondown.read()==1){
        //wait(0.05); //チャタリングをスルーする場合はもう一回if文書く必要あり。
        armdown=0.5;
        armup=0;
        
           if(buttondown.read()==0){
               armdown=0;
               armup=0.5;
               wait(300);//この時間に元の位置に戻る
            }
        }
       else{
                armdown=0;
                armup=0;
            }
    }
}
