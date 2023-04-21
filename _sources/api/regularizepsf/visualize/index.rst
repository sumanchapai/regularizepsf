:py:mod:`regularizepsf.visualize`
=================================

.. py:module:: regularizepsf.visualize


Module Contents
---------------

.. py:function:: visualize_patch_counts(patch_collection: regularizepsf.fitter.PatchCollectionABC, ax: Optional[matplotlib.axes.Axes] = None, label_pixel_bounds: bool = False) -> matplotlib.axes.Axes

   Utility to visualize the number of stars identified within each patch

   :param patch_collection: A patch collection, such as that returned by
                            `CoordinatePatchCollection.find_stars_and_average`.
   :type patch_collection: PatchCollectionABC
   :param ax: An Axes object on which to plot. If not provided, a new Figure will be
              generated.
   :type ax: matplotlib.axes.Axes
   :param label_pixel_bounds: If True, the axes of the plot will be labeled with the pixel range
                              spanned by each patch.
   :type label_pixel_bounds: bool


.. py:function:: visualize_PSFs(psfs: regularizepsf.corrector.ArrayCorrector, corrected: Optional[regularizepsf.fitter.PatchCollectionABC] = None, all_patches: bool = False, region_size: int = 0, label_pixel_bounds: bool = False, fig: Optional[matplotlib.figure.Figure] = None, fig_scale: float = 1, colorbar_label: str = 'Normalized brightness', axis_border_color: str = 'white', imshow_args: dict = {}) -> matplotlib.figure.Figure

   Utility to visualize estimated PSFs.

   Accepts an `ArrayCorrector`, which contains the estimated PSFs across the
   image.

   This utility can also produce a "before and after" visualization. To do
   this, apply your `ArrayCorrector` to your image set, and then run
   `CoordinatePatchCollection.find_stars_and_average` on your corrected
   images. This will estimated the PSF of your corrected images. Pass this
   `CoordinatePatchCollection` as the `corrected` argument to this function.

   :param psfs: An `ArrayCorrector` containing the estimated PSFs
   :type psfs: ArrayCorrector
   :param corrected: A `CoordinatePatchCollection` computed on the corrected set of images
   :type corrected: PatchCollectionABC
   :param all_patches: PSFs are estimated in a grid of overlapping patches, with each image
                       pixel being covered by four patches. If `True`, all of these patches
                       are plotted, which can be useful for diagnosing the estimated PSFs. If
                       `False`, only a fourth of all patches are plotted (every other patch in
                       both x and y), which can produce simpler illustrations.
   :type all_patches: boolean
   :param region_size: The width of the central region of each patch to plot, or 0 to plot
                       each entire patch. If the PSFs were computed with a `psf_size` less
                       than `patch_size`, it may be convenient to set `region_size=psf_size`,
                       to omit the empty edges of each patch.
   :type region_size: int
   :param label_pixel_bounds: If True, the axes of the plot will be labeled with the pixel range
                              spanned by each patch.
   :type label_pixel_bounds: bool
   :param fig: A Figure on which to plot. If not provided, one will be created.
   :type fig: matplotlb.figure.Figure
   :param fig_scale: If `fig` is not provided, scale the generated Figure up or down by this
                     factor.
   :type fig_scale: float
   :param colorbar_label: The label to show on the colorbar
   :type colorbar_label: str
   :param axis_border_color: The color to use for the lines separating the patch plots.
   :type axis_border_color: str
   :param imshow_args: Additional arguments to pass to each `plt.imshow()` call
   :type imshow_args: dict

   :returns: **fig** -- The generated figure
   :rtype: matplotlib.figure.Figure


.. py:function:: visualize_transfer_kernels(psfs: regularizepsf.corrector.ArrayCorrector, alpha: float, epsilon: float, all_patches: bool = False, region_size: int = 0, label_pixel_bounds: bool = False, fig: Optional[matplotlib.figure.Figure] = None, fig_scale: float = 1, colorbar_label: str = 'Transfer kernel amplitude', axis_border_color: str = 'black', imshow_args: dict = {}) -> matplotlib.figure.Figure

   Utility to compute and visualize transfer kernels.

   Accepts an `ArrayCorrector`, which contains the estimated PSFs across the
   image. These PSFs, and the target PSF, will be used to compute each
   transfer kernel.

   :param psfs: An `ArrayCorrector` containing the estimated PSFs and target PSF
   :type psfs: ArrayCorrector
   :param alpha: Values used in computing the regularized reciprocal of the computed
                 PSFs. Provide the same values that you would pass to
                 `ArrayCorrector.correct_image`.
   :type alpha: float
   :param epsilon: Values used in computing the regularized reciprocal of the computed
                   PSFs. Provide the same values that you would pass to
                   `ArrayCorrector.correct_image`.
   :type epsilon: float
   :param all_patches: PSFs are estimated in a grid of overlapping patches, with each image
                       pixel being covered by four patches. If `True`, all of these patches
                       are plotted, which can be useful for diagnosing the computed PSFs. If
                       `False`, only a fourth of all patches are plotted (every other patch in
                       both x and y), which can produce simpler illustrations.
   :type all_patches: boolean
   :param region_size: The width of the central region of each patch to plot, or 0 to plot
                       each entire patch. If the PSFs were computed with a `psf_size` less
                       than `patch_size`, it may be convenient to set `region_size=psf_size`,
                       to omit the empty edges of each patch.
   :type region_size: int
   :param label_pixel_bounds: If True, the axes of the plot will be labeled with the pixel range
                              spanned by each patch.
   :type label_pixel_bounds: bool
   :param fig: A Figure on which to plot. If not provided, one will be created.
   :type fig: matplotlb.figure.Figure
   :param fig_scale: If `fig` is not provided, scale the generated Figure up or down by this
                     factor.
   :type fig_scale: float
   :param colorbar_label: The label to show on the colorbar
   :type colorbar_label: str
   :param axis_border_color: The color to use for the lines separating the patch plots.
   :type axis_border_color: str
   :param imshow_args: Additional arguments to pass to each `plt.imshow()` call
   :type imshow_args: dict

   :returns: **fig** -- The generated figure
   :rtype: matplotlib.figure.Figure


