```cpp
#include <iostream>
#include <opencv2/imgcodecs.hpp>
#include <opencv/imgproc.hpp>
#include <opencv2/highhui.hpp>


using namespace std;
using namespace cv;

Mat imgGray, imgBlur, imgCanny, imgDil, imgErode;

void getContours(Mat imgDil, Mat img){
     
     vector<vector<Point>> contours;
     vector<Vec4i> hierarchy;
    
     findContours(imgDil,contours,hierarchy,RETR_EXTERNAL, CHAIN_APPROX_SIMPLE);
     //drawContours(img,contours,-1,Scalar(255,0,255),2);

//调参
     vector<vector<Point>> conPoly(contours.size());
     vector<Rect> boundRect(contours.size());
     string objectType;
     for (int i =0; i < contours.size(); i++){
    
         int area= contoursArea(contours[i]);
         cout<<area <<endl;
         
         if (area>1000){
            float peri=arcLength(contours[i],true);
            approxPolyDP(contours[i],conPoly[i],0.02*peri,true);//若不用随机数则乘以当前参数
            
            cout << conPoly[i].size() << endl;
            boundRect[i] = boundingRect(conPoly[i]);
            
            int objCor = (int)conPoly[i].size();

           //仅对于low
            if (objCor == 3){objectType = "Triangle";}
            if (objCor == 4){objectType = "Rectangle";}
            if (objCor == 5){objectType = "Pentagon";}
            if (objCor == 6){objectType = "Hexagon";}
            if (objCor > 6){objectType = "Circle";}


            drawContours(img,contours,i,Scalar(255,0,255),2);
            rectangle(img,boundRect[i].tl(),boundRect[i].br(),Scalar(0,255,0),5);
            putText(img,objectType,{boundRect[i].x,boundRect[i].y-5},FONT_HERSHEY_PLAIN, 1,Scalar(0,69,255),1);



         }

}
    

}


void main(){

     string path = "Resources/shapes.png";
     Mat img = imread(path);

     //preprocessing
     cvtColor(img,imgGray,COLOR_BGR2GRAY);
     GaussianBlur(imgGray,imgBlur, Size(3,3),3,0);
     Canny(imgBlur, imgCanny,25,75);
     Mat kernel = getStructuringElement(MORPH_RECT,Size(3,3));
     dilate(imgCanny, imgDil, kernel);

     getContours(imgDil,img);

     imshow("Image",img);
     waitKey(0);
}
```
