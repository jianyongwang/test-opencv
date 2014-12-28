#include <cv.h>
#include <highgui.h>
using namespace std;
using namespace cv;
int main()
{
	vector<vector<Point>> contours;
	int cmin = 100;
	int cmax = 1000;
	Mat image = imread("..\\..\\binaryGroup.bmp",CV_LOAD_IMAGE_GRAYSCALE);
	Mat group = imread("..\\..\\group.jpg", CV_LOAD_IMAGE_GRAYSCALE);
	findContours(image, contours, CV_RETR_EXTERNAL, CV_CHAIN_APPROX_NONE);
	vector<vector<Point>>::const_iterator itc = contours.begin();
	while (itc!=contours.end())
	{
		if (itc->size() < cmin || itc->size() > cmax)
			itc = contours.erase(itc);
		else
			itc++;
	}

	
	Mat result(image.size(), CV_8U, Scalar(255));
	drawContours(result, contours, -1, Scalar(0), 2);
	//测试最小包围
	float radius;
	Point2f center;
	minEnclosingCircle(Mat(contours[1]), center, radius);
	circle(result, Point(center),static_cast<int>(radius), Scalar(0), 2);
	

	//包围盒
	Rect rect0 = boundingRect(Mat(contours[0]));
	rectangle(result, rect0, Scalar(0), 2);


	//测试多边形近似
	vector<Point>poly;
	approxPolyDP(Mat(contours[2]), poly, 20, true);
	vector<Point>::const_iterator itp = poly.begin();
	while (itp != (poly.end() - 1))
	{
		line(result, *itp, *(itp + 1), Scalar(20), 2);
		itp++;
	}
	line(result, *itp, *(poly.end() - 1),Scalar(20),2); 


	//凸包
	vector<	Point> hull;
	convexHull(Mat(contours[3]) ,hull);
	itp=hull.begin();
	while (itp != hull.end() - 1)
	{
		line(result, *itp, *(itp + 1), Scalar(20), 2);
		itp++;
	}
	line(result, *itp, *(hull.end() - 1), Scalar(20), 2);


	imshow("contours", result);
	waitKey(0);
	drawContours(group, contours, -1, Scalar(255), 2);
	imshow("contours", group);
	waitKey(0);
	system("pause");
	cout<<test<<endl;
	return 0;
}
