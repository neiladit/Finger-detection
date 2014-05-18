 #include "opencv2/highgui/highgui.hpp"
 #include "opencv2/imgproc/imgproc.hpp"
 #include <iostream>
 #include <stdio.h>
 #include <stdlib.h>
 //#include "skin.cpp"

 using namespace cv;
 using namespace std;
 
 int loop=0;
 Mat src; Mat src_gray,srcf;
 int thresh = 100;
 int max_thresh = 255;
 RNG rng(12345);
 int erosion_elem = 0;
 int erosion_size = 0;
 int dilation_elem = 0;
 int dilation_size = 0;
 int const max_elem = 2;
 int const max_kernel_size = 21;
 Mat Erosion( int, void*,Mat );
 Mat Dilation( int, void*,Mat );
 int k=0;


 class SkinDetector
{
public:
SkinDetector(void);
~SkinDetector(void);

cv::Mat getSkin(cv::Mat input);

private:
int Y_MIN;
int Y_MAX;
int Cr_MIN;
int Cr_MAX;
int Cb_MIN;
int Cb_MAX;
};

SkinDetector::SkinDetector(void)
{
//YCrCb threshold
// You can change the values and see what happens
Y_MIN  = 80;
Y_MAX  = 255;
Cr_MIN = 133;
Cr_MAX = 173;
Cb_MIN = 77;
Cb_MAX = 127;
}

SkinDetector::~SkinDetector(void){}

//this function will return a skin masked image
cv::Mat SkinDetector::getSkin(cv::Mat input)
{
cv::Mat skin;
//first convert our RGB image to YCrCb
cv::cvtColor(input,skin,cv::COLOR_BGR2YCrCb);

//uncomment the following line to see the image in YCrCb Color Space
//cv::imshow("YCrCb Color Space",skin);

//filter the image in YCrCb color space
cv::inRange(skin,cv::Scalar(Y_MIN,Cr_MIN,Cb_MIN),cv::Scalar(Y_MAX,Cr_MAX,Cb_MAX),skin);

return skin;
}

Mat Erosion( int, void*,Mat src_gray )
{
  int erosion_type;Mat erosion_dst;
  if( erosion_elem == 0 ){ erosion_type = MORPH_RECT; }
  else if( erosion_elem == 1 ){ erosion_type = MORPH_CROSS; }
  else if( erosion_elem == 2) { erosion_type = MORPH_ELLIPSE; }

  Mat element = getStructuringElement( erosion_type,
                                       Size( 2*erosion_size + 1, 2*erosion_size+1 ),
                                       Point( erosion_size, erosion_size ) );

  /// Apply the erosion operation
  erode( src_gray, erosion_dst, element );
  //imshow( "Erosion Demo", erosion_dst );
  return erosion_dst;
}

Mat Dilation( int, void*,Mat src_gray2 )
{
  int dilation_type;Mat dilation_dst;
  if( dilation_elem == 0 ){ dilation_type = MORPH_RECT; }
  else if( dilation_elem == 1 ){ dilation_type = MORPH_CROSS; }
  else if( dilation_elem == 2) { dilation_type = MORPH_ELLIPSE; }

  Mat element = getStructuringElement( dilation_type,
                                       Size( 2*dilation_size + 1, 2*dilation_size+1 ),
                                       Point( dilation_size, dilation_size ) );
  /// Apply the dilation operation
  dilate( src_gray2, dilation_dst, element );
  //imshow( "Dilation Demo", dilation_dst );
  return dilation_dst;
}

 /// Function header
 void thresh_callback(int, void*,Mat );

int findBiggestContour(vector<vector<Point> > contours)
{ int indexOfBiggestContour = -1; int sizeOfBiggestContour = 0; for (int i = 0; i < contours.size(); i++){ if(contours[i].size() > sizeOfBiggestContour){ sizeOfBiggestContour = contours[i].size(); indexOfBiggestContour = i; } } return indexOfBiggestContour; }

/** @function main */
int main( int argc, char** argv )
 {
   VideoCapture cap(0); // open the default camera

   if(!cap.isOpened()) // check if we succeeded

   return -1;
   /// Load source image and convert it to gray
   for(;;)
{
   
   cap >> src;
   /// Convert image to gray and blur it
   //cvtColor( src, src_gray, CV_BGR2GRAY );
   
   SkinDetector mySkinDetector;
   Mat src_skin=mySkinDetector.getSkin(src);   
   imshow( "after skin detect", src_skin );
   
   blur( src_skin,src_gray, Size(3,3) );
  
   /// Create Window
   
   //createTrackbar( " Threshold:", "Source", &thresh, max_thresh, thresh_callback );

   for(int a=0;a<100;a++)
   {src_gray=Dilation( 0, 0 ,src_gray);}
   srcf=src_gray;
  
   thresh_callback( 0, 0, srcf);

   if(waitKey(30)>=0) break;
}
 

   
   return(0);
 }

 /** @function thresh_callback */
 void thresh_callback(int, void*,Mat  src_inp)
 {
   Mat src_copy = src.clone();
   Mat threshold_output;
   vector<vector<Point> > contours;
   vector<Vec4i> hierarchy;

   /// Detect edges using Threshold
   threshold( src_inp, threshold_output, thresh, 255, THRESH_BINARY );

   /// Find contours
   findContours( threshold_output, contours, hierarchy, CV_RETR_TREE, CV_CHAIN_APPROX_SIMPLE, Point(0, 0) );
   int s = findBiggestContour(contours);
   /*Mat drawing = Mat::zeros( src.size(), CV_8UC1 );
   drawContours( drawing, contours, s, Scalar(255), -1, 8, hierarchy, 0, Point() );
   imshow("drw", drawing);*/
  
        vector<vector<int> >hull( contours.size() );
        vector<vector<Vec4i> > convDef(contours.size() );
        vector<vector<Point> > hull_points(contours.size());
        vector<vector<Point> > defect_points(contours.size());
        vector<vector<Point> > approx(contours.size());

      Mat drawing = Mat::zeros( threshold_output.size(), CV_8UC3 );
      Mat drawing2 = Mat::zeros( threshold_output.size(), CV_8UC3 );
      Mat drawingh = Mat::zeros( threshold_output.size(), CV_8UC3 );


 vector<Moments> mu(contours.size() );
     for( int i = 0; i < contours.size(); i++ )
      { mu[i] = moments( contours[i], false ); }

       ///  Get the mass centers:
       vector<Point2f> mc( contours.size() );
       for( int i = 0; i < contours.size(); i++ )
       {if(contourArea(contours[i])>15000){ mc[i] = Point2f( mu[i].m10/mu[i].m00 , mu[i].m01/mu[i].m00 ); }}

   /// Draw contours + hull results
    if(contours.size()>0)
    {

        for( int i = 0; i < contours.size(); i++ )
        {
            if(contourArea(contours[i])>20000)
            {
                convexHull( contours[i], hull[i], false );
                convexityDefects( contours[i],hull[i], convDef[i]);

                // start_index, end_index, farthest_pt_index, fixpt_depth
  
                for(int k=0;k<hull[i].size();k++)
                {        
                    int ind=hull[i][k];
                    hull_points[i].push_back(contours[i][ind]);
                    approxPolyDP(hull_points[i],approx[i],3,true);
                }
       for(int k=0;k<convDef[i].size();k++)
                {           
                    if(convDef[i][k][3]>20*256 && contourArea(contours[i])>20000)
                    {
                        int ind_0=convDef[i][k][0];
                        int ind_1=convDef[i][k][1];
                        int ind_2=convDef[i][k][2];
                        defect_points[i].push_back(contours[i][ind_2]);
                        cv::circle(drawing,contours[i][ind_0],5,Scalar(0,255,0),-1);
                        cv::circle(drawing,contours[i][ind_1],5,Scalar(0,255,0),-1);
                        cv::circle(drawing,contours[i][ind_2],5,Scalar(0,0,255),-1);
                        cv::line(drawing,contours[i][ind_2],contours[i][ind_0],Scalar(0,0,255),1);
                        cv::line(drawing,contours[i][ind_2],contours[i][ind_1],Scalar(0,0,255),1);
                        cv::circle(src,mc[i],5,Scalar(0,255,0),-1);
                        /*cv::circle(src,contours[i][ind_1],5,Scalar(0,255,0),-1);
                        cv::circle(src,contours[i][ind_2],5,Scalar(0,0,255),-1);
                        cv::line(src,contours[i][ind_2],contours[i][ind_0],Scalar(0,0,255),1);
                        cv::line(src,contours[i][ind_2],contours[i][ind_1],Scalar(0,0,255),1);*/
                    }
                }
  }}}
   
   
      
      for( int i = 0; i< contours.size(); i++ )
      {if(i==s)
        {Scalar color = Scalar( 255, 0, 0 );Scalar coloro = Scalar( 0, 0, 255 );
        drawContours( drawing, contours, i, color, 1, 8, vector<Vec4i>(), 0, Point() );
        drawContours( drawing, hull_points, i, color, 1, 8, vector<Vec4i>(), 0, Point() );
        drawContours( drawing2, contours, i, color, 1, 8, vector<Vec4i>(), 0, Point() );
        drawContours( drawingh, approx, i, coloro, 1, 8, vector<Vec4i>(), 0, Point() );
        drawContours( src, approx, i, color, 1, 8, vector<Vec4i>(), 0, Point() );}
        //circle( src, mc[i], 4, color, -1, 8, 0 );
      }
   /// Show in a window
 
   imshow("Original",src);
   imshow( "Hull",drawingh);
   imshow( "Contours + hull + convexity defects", drawing);
   imshow( "Contours", drawing2 );
   
   
   
 }
