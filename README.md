
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <windows.h>
#include <math.h>
int main() {
   BITMAPFILEHEADER bmpFile;
   BITMAPINFOHEADER bmpInfo;
   FILE* inputFile1 = NULL;
   inputFile1 = fopen("AICenter.bmp", "rb");
   fread(&bmpFile, sizeof(BITMAPFILEHEADER), 1, inputFile1);
   fread(&bmpInfo, sizeof(BITMAPINFOHEADER), 1, inputFile1);

   int width = bmpInfo.biWidth;
   int height = bmpInfo.biHeight;
   int size = bmpInfo.biSizeImage;
   int bitCnt = bmpInfo.biBitCount;
   int stride = (((bitCnt / 8) * width) + 3) / 4 * 4;
   printf("W: %d(%d)\nH: %d\nS: %d\nD: %d\n", width, stride, height, size, bitCnt);

   unsigned char* inputImg1 = NULL, * Y1 = NULL, * Y2 = NULL;
   inputImg1 = (unsigned char*)calloc(size, sizeof(unsigned char));
   Y1 = (unsigned char*)calloc(size, sizeof(unsigned char));
   Y2 = (unsigned char*)calloc(size, sizeof(unsigned char));
   fread(inputImg1, sizeof(unsigned char), size, inputFile1);

   double Y, Cb, Cr, R, G, B;
   double mse = 0, psnr;
   for (int j = 0; j < height; j++) {
	  for (int i = 0; i < width; i++) {
		 Y1[j * stride + 3 * i + 0] = inputImg1[j * stride + 3 * i + 0];
		 Y1[j * stride + 3 * i + 1] = inputImg1[j * stride + 3 * i + 1];
		 Y1[j * stride + 3 * i + 2] = inputImg1[j * stride + 3 * i + 2];
	  }
   }
   for (int j = 0; j < height; j++) {
	  for (int i = 0; i < width; i++) {
		 Y2[j * stride + 3 * i + 0] = inputImg1[j * stride + 3 * i + 0];
		 Y2[j * stride + 3 * i + 1] = inputImg1[j * stride + 3 * i + 1];
		 Y2[j * stride + 3 * i + 2] = inputImg1[j * stride + 3 * i + 2];
	  }
   }

   for (int j = 0; j < height; j++) {
	  for (int i = 0; i < width; i++) {
		 mse += (double)((Y2[j * width + i] - Y1[j * width + i]) * (Y2[j * width + i] - Y1[j * width + i]));
	  }
   }
   mse /= (width * height);
   psnr = mse != 0.0 ? 10.0 * log10(255 * 255 / mse) : 99.99;
   printf("MSE= %.2lf\nPSNR=%.2lf dB\n", mse, psnr);

}
