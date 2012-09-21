========
views.py
========

This is probably where you have to change the most code

Ordering on translated fields
-----------------------------

If you want to order on translated fields, you have to use the :meth:`using_translations`
on the manager::

	ordered_by_name = MyModel.objects.using_translations().order_by('name')

Querying related fields on non-translated models
------------------------------------------------

Given this model::

	class Hobbies(TranslatedModel):
		user = models.ForeignKey('auth.User')

		translations = TranslatedField(
			name = models.CharField(max_length=20),
		)

If you want to have a translated list of hobbies from user ``a``, you need to
use :func:`get_translation_aware_manager`::

	hobbies =
