:py:mod:`regularizepsf`
=======================

.. py:module:: regularizepsf


Submodules
----------
.. toctree::
   :titlesonly:
   :maxdepth: 1

   corrector/index.rst
   exceptions/index.rst
   fitter/index.rst
   psf/index.rst
   visualize/index.rst


Package Contents
----------------

.. py:class:: ArrayCorrector(evaluations: dict[Any, numpy.ndarray], target_evaluation: numpy.ndarray)

   Bases: :py:obj:`CorrectorABC`

   A PSF corrector that is evaluated as array patches


   Initialize an ArrayCorrector

   :param evaluations:
                       evaluated version of the PSF as they vary over the image,
                           keys should be (x, y) of the lower left
                           pixel of each patch. values should be the `np.ndarray`
                           that corresponds to that patch
   :type evaluations: dict
   :param target_evaluation: evaluated version of the target PSF
   :type target_evaluation: np.ndarray

   .. py:method:: correct_image(image: numpy.ndarray, size: int = None, alpha: float = 0.5, epsilon: float = 0.05) -> numpy.ndarray

      PSF correct an image according to the model

      :param image: image to be corrected
      :type image: 2D float np.ndarray
      :param size: how big to make the patches when correcting an image,
                   only used for FunctionalCorrector
      :type size: int
      :param alpha: controls the “hardness” of the transition from amplification
                    to attenuation, see notes
      :type alpha: float
      :param epsilon: controls the maximum of the amplification, see notes
      :type epsilon: float

      :returns: a image that has been PSF corrected
      :rtype: np.ndarray


   .. py:method:: __getitem__(xy: Tuple[int, int]) -> numpy.ndarray


   .. py:method:: save(path: str) -> None

      Save the model to a file.

      :param path: where to save the model, suggested extension is ".psf"
      :type path: str or `pathlib.Path`

      :rtype: None


   .. py:method:: load(path: str) -> ArrayCorrector
      :classmethod:

      Loads a model from the path

      :param path: where to load the model from, suggested extension is ".psf"
      :type path: str or `pathlib.Path`


   .. py:method:: simulate_observation(image: numpy.ndarray) -> numpy.ndarray

      Simulates on a star field what an observation using this PSF looks like

      :param image: image of point source stars to simluate PSF for
      :type image: 2D float np.ndarray

      :returns: an image with the PSF applied
      :rtype: np.ndarray



.. py:class:: FunctionalCorrector(psf: regularizepsf.psf.PointSpreadFunctionABC, target_model: SimplePSF | None)

   Bases: :py:obj:`CorrectorABC`

   A version of the PSF corrector that stores the model as a set of functions.
   For the actual correction, the functions must first
   be evaluated to an ArrayCorrector.

   Initialize a FunctionalCorrector

   :param psf: the model describing the psf for each patch of the image
   :type psf: SimplePSF or VariedPSF
   :param target_model: the target PSF to use to establish uniformity across the image
   :type target_model: SimplePSF or None

   .. py:property:: is_variable
      :type: bool

      



      returns: True if the PSF model is varied (changes across the field-of-view)
                and False otherwise
      :rtype: bool

   .. py:method:: evaluate_to_array_form(x: numpy.ndarray, y: numpy.ndarray, size: int) -> ArrayCorrector

      Evaluates a FunctionalCorrector to an ArrayCorrector

      :param x: the first dimension coordinates to evaluate over
      :type x: np.ndarray
      :param y: the second dimension coordinates to evaluate over
      :type y: np.ndarray
      :param size: how large the patches in the PSF correction model shouuld be
      :type size: int

      :returns: an array evaluated form of this PSF corrector
      :rtype: ArrayCorrector


   .. py:method:: correct_image(image: numpy.ndarray, size: int, alpha: float = 0.5, epsilon: float = 0.05) -> numpy.ndarray

      PSF correct an image according to the model

      :param image: image to be corrected
      :type image: 2D float np.ndarray
      :param size: how big to make the patches when correcting an image,
                   only used for FunctionalCorrector
      :type size: int
      :param alpha: controls the “hardness” of the transition from amplification
                    to attenuation, see notes
      :type alpha: float
      :param epsilon: controls the maximum of the amplification, see notes
      :type epsilon: float

      :returns: a image that has been PSF corrected
      :rtype: np.ndarray


   .. py:method:: save(path: str) -> None

      Save the model to a file.

      :param path: where to save the model, suggested extension is ".psf"
      :type path: str or `pathlib.Path`

      :rtype: None


   .. py:method:: load(path: str) -> FunctionalCorrector
      :classmethod:

      Loads a model from the path

      :param path: where to load the model from, suggested extension is ".psf"
      :type path: str or `pathlib.Path`


   .. py:method:: simulate_observation(image: numpy.ndarray, size: int) -> numpy.ndarray

      Simulates on a star field what an observation using this PSF looks like

      :param image: image of point source stars to simluate PSF for
      :type image: 2D float np.ndarray
      :param size: the PSF will be evaluated to size x size pixels box
      :type size: int

      :returns: an image with the PSF applied
      :rtype: np.ndarray



.. py:function:: calculate_covering(image_shape: tuple[int, int], size: int) -> numpy.ndarray

   Determines the grid of overlapping neighborhood patches.

   :param image_shape: shape of the image we plan to correct
   :type image_shape: tuple of 2 ints
   :param size: size of the square patches we want to create
   :type size: int

   :returns: an array of shape Nx2 where return[:, 0]
             are the x coordinate and return[:, 1] are the y coordinates
   :rtype: np.ndarray


.. py:class:: CoordinatePatchCollection(patches: dict[Any, numpy.ndarray], counts: Optional[dict[Any, int]] = None)

   Bases: :py:obj:`PatchCollectionABC`

   A representation of a PatchCollection that operates
   on pixel coordinates from a set of images

   .. py:method:: extract(images: list[numpy.ndarray], coordinates: list[CoordinateIdentifier], size: int) -> PatchCollectionABC
      :classmethod:

      Construct a PatchCollection from a set of images
      using the specified coordinates and patch size

      :param images: the images loaded
      :type images: list of np.ndarrays
      :param coordinates:
                          A list of coordinates for the lower left pixel of each patch,
                              specified in each type of PatchCollection
      :type coordinates: list
      :param size: size of one side of the square patches extracted
      :type size: int

      :returns: the square patches extracted into a PatchCollection
      :rtype: np.ndarray


   .. py:method:: find_stars_and_average(images: list[str] | np.ndarray | Generator, psf_size: int, patch_size: int, interpolation_scale: int = 1, average_mode: str = 'median', percentile: float = 10, star_threshold: int = 3, star_mask: Optional[list[str] | np.ndarray | Generator] = None, hdu_choice: int = 0) -> CoordinatePatchCollection
      :classmethod:

      Loads a series of images, finds stars in each,
          and builds a CoordinatePatchCollection with averaged stars

      :param images: The images to be processed. Can be a list of FITS filenames, a
                     numpy array of shape (n_images, ny, nx), or a Generator that yields
                     each data array in turn.
      :type images: List[str] or np.ndarray or Generator
      :param psf_size: size of the PSF model to use
      :type psf_size: int
      :param patch_size: square size that each PSF model applies to
      :type patch_size: int
      :param interpolation_scale:
                                  if >1, the image are first scaled by this factor.
                                      This results in stars being aligned at a subpixel scale
      :type interpolation_scale: int
      :param average_mode: "median", "percentile", or "mean": determines how patches are
                           combined
      :type average_mode: str
      :param percentile: If `average_mode` is `"percentile"`, use this percentile value
                         (from 0 to 100)
      :type percentile: float
      :param star_threshold:
                             SEP's threshold for finding stars. See `threshold`
                                 in https://sep.readthedocs.io/en/v1.1.x/api/sep.extract.html#sep-extract
      :type star_threshold: int
      :param star_mask: Masks to apply during star-finding. Can be a list of FITS filenames, a
                        numpy array of shape (n_images, ny, nx), or a Generator that yields
                        each mask array in turn. Where the mask pixel is `True`, the
                        corresponding data array pixel will not be selected as a star. See
                        `mask` in
                        https://sep.readthedocs.io/en/v1.1.x/api/sep.extract.html#sep-extract
                        for more details.
      :type star_mask: List[str] or np.ndarray or Generator
      :param hdu_choice:
                         Which HDU from each image will be used,
                             default of 0 is most common but could be 1 for compressed images
      :type hdu_choice: int

      :returns: An averaged star model built from the provided images
      :rtype: CoordinatePatchCollection

      .. rubric:: Notes

      Using an `interpolation_scale` other than 1
          for large images can dramatically slow down the execution.


   .. py:method:: average(corners: numpy.ndarray, patch_size: int, psf_size: int, mode: str = 'median', percentile: float = 10) -> PatchCollectionABC

      Construct a new PatchCollection where patches
      lying inside a new grid are averaged together

      :param corners: an Nx2 `np.ndarray` of the lower left corners of the new patch grid
      :type corners: np.ndarray
      :param step: how far apart each corner patch is
      :type step: int
      :param size: dimension of the new (size, size) shaped square patches
      :type size: int
      :param mode: either average using "mean" or "median"
      :type mode: str

      :returns: a PatchCollection where data is sampled at the new grid
      :rtype: PatchCollectionABC


   .. py:method:: fit(base_psf: regularizepsf.psf.SimplePSF, is_varied: bool = False) -> regularizepsf.psf.PointSpreadFunctionABC
      :abstractmethod:

      :param base_psf:
      :param is_varied:


   .. py:method:: to_array_corrector(target_evaluation: numpy.array) -> regularizepsf.corrector.ArrayCorrector

      Converts a patch collection that has been averaged into an ArrayCorrector

      :param target_evaluation: the evaluation of the Target PSF
      :type target_evaluation: np.ndarray

      :returns: An array corrector that can be used to correct PSFs
      :rtype: ArrayCorrector



.. py:function:: simple_psf(arg: Any = None) -> SimplePSF


.. py:function:: varied_psf(base_psf: SimplePSF = None) -> VariedPSF


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


